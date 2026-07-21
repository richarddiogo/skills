---
name: smoke-anchor
description: Provar que algo que você acabou de construir realmente funciona, com uma barreira objetiva barata (âncora) que o agente repete até passar. Use ao terminar/entregar uma função, endpoint, componente ou mudança — em vez de dizer "parece ok" ou escrever um smoke test vago. Cobre escolher âncora-oráculo vs âncora-marco, plantar o observável certo, e martelar até verde com parada dura.
---

# Smoke Anchor — prove que funciona, não confie que funciona

Você acabou de construir/mudar algo. Antes de declarar pronto, plante uma **âncora**: a menor prova objetiva de que aquilo funciona, que a máquina checa sozinha (exit code, sem opinião), e contra a qual você pode iterar até ficar verde. Isto substitui "parece ok", "não deu erro" e o smoke test improvisado.

## A regra (o que muita gente erra)

> **Uma âncora = o único observável que vira de falso para verdadeiro quando a mudança funciona.**

Não é "testa tudo" (fica lento e não localiza nada). Não é "não deu erro" (passa mesmo quebrado). É **o único fato que seria falso se estivesse quebrado.** Um por mudança.

## Passo 1 — Oráculo ou marco?

Todo "será que funcionou?" é um dos dois tipos (às vezes os dois):

| Tipo | Prova | Use para |
|---|---|---|
| 🎯 **Âncora-oráculo** | a saída == um valor conhecido-verdadeiro | cálculo, transformação, regra de negócio |
| 📍 **Âncora-marco** | um evento observável aconteceu | efeito colateral: renderizou, logou, gravou no banco, API respondeu, schema validou |

Oráculo responde *"o valor bate?"*. Marco responde *"aconteceu?"*.

## Passo 2 — Plante a âncora

Escolha **o menor observável que seria falso se estivesse quebrado** e escreva a checagem objetiva. Mapa rápido:

| Situação | Âncora | Ferramenta de mercado |
|---|---|---|
| Função pura (entra X → sai Y) | 🎯 `f(x) == esperado` | pytest + fixture golden; **Hypothesis** (gera tiers sozinho) |
| API — formato da resposta | 📍 resposta valida no schema | **Pydantic** / jsonschema; **Pact** (contract testing) |
| API — valores da resposta | 🎯 `GET /x/1 → name == "foo"` | pytest + TestClient/httpx |
| Gravou no banco / publicou evento | 📍 a linha existe / o evento saiu | pytest + query de verificação |
| Componente de front renderizou | 📍 `data-testid` presente e visível | Testing Library / Playwright / render headless |
| Regra de negócio na tela | 🎯 valor na tela == função unitária (oráculo) | asserção sobre o testid do valor |
| Agente numa pipeline | 📍 saída valida no schema, por fronteira | structured output / Pydantic |

Para oráculos de cálculo, **congele o esperado** num fixture (`fixtures/caso.json` com input + output revisado uma vez por humano), e teste tiers: pequeno (1), médio (5), limite (máx razoável). *Property-based* (Hypothesis) automatiza esses tiers.

## Passo 3 — Martele até verde, com parada dura

Rode a âncora. Vermelho → conserte → rode de novo. Verde → pronto. **Regras da parada** (senão queima token à toa):

- **Máx ~3-5 tentativas.** Ao atingir, PARE e pergunte no chat se continua — não itere pra sempre.
- **Erro repetido = pare antes.** Se o mesmo erro aparece 2-3 vezes, quase sempre a âncora está errada ou a abordagem é beco sem saída. Não martele; investigue ou pergunte.
- **Loop que nunca fica verde ≠ código ruim.** Antes de iterar, rode a âncora UMA vez à mão e confirme que ela *consegue* ficar verde (seletor certo, build compila). Barreira impossível de satisfazer é pior que barreira nenhuma.
- **Verifique o verificador.** Um verde de primeira não prova que a âncora presta — âncora fraca também passa. Antes de confiar, confirme que ela fica *vermelha* num input sabidamente quebrado. Verde só conta depois que você viu a âncora reprovar algo.

## Cuidado: a âncora guarda forma, não sentido

Uma âncora-schema confirma que a saída é *bem-formada* — não que está *certa*. Um kanban pode validar 100% no contrato com os cards nas colunas erradas. Schema-verde é **necessário, não suficiente**: cobre estrutura, referências e enums; não cobre se a decisão faz sentido. Para sentido, adicione 2-3 **cenários golden** (input conhecido → forma de saída aceitável) ou LLM-juiz — só depois que o schema estiver verde.

**Em pipeline multi-agente o valor se compõe.** Se cada agente cumpre o contrato 95% das vezes, 30 agentes juntos dão 0,95³⁰ ≈ 21% — ~4 em 5 rodadas têm uma violação em algum lugar. Uma âncora por fronteira faz a falha estourar onde nasceu, com mensagem exata, em vez de aparecer 25 agentes depois. O ganho cresce com o número de agentes; num agente só, é quase overhead.

## Por que isto é uma skill (e não "a LLM já sabe")

O agente é capaz de fazer, mas por padrão não faz **sempre**, no **formato certo**, nem **se para com honestidade** — ele é otimista avaliando o próprio trabalho e se convence de que terminou (*Ralph Wiggum*). A skill dá disciplina (âncora toda vez), formato (a regra acima) e auto-ceticismo (barreira objetiva + parada dura) — não QI.

## Escopo

- Isto é o **teste que prova** + o **mini-loop de iterar até verde** com você na cadeira. Não é um loop autônomo agendado — para isso, veja `build-loop`.
- Invariantes sensíveis (segurança, isolamento de dados, migração): a âncora dispara o teste, mas o **humano decide** o conserto — não deixe o agente mexer sozinho ali.
