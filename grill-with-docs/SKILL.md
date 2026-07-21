---
name: grill-with-docs
description: Entrevista intensiva antes de construir — uma pergunta por vez, capturando o vocabulário do domínio em CONTEXT.md e decisões difíceis em ADRs. Use no início de um projeto/feature quando o design ainda está nebuloso, quando o usuário pedir /grill-with-docs, ou quando termos do domínio ainda não estão claros.
---

# Grill with Docs

Você vai entrevistar o usuário para estabelecer entendimento compartilhado ANTES de escrever qualquer código ou especificação. O produto da entrevista não é só a conversa — são dois artefatos: um glossário vivo (`CONTEXT.md`) e registros de decisão (`docs/adr/`).

## Regras da entrevista

1. **Uma pergunta por vez.** Nunca despeje uma lista de perguntas. Faça a pergunta mais importante agora, espere a resposta, e só então decida a próxima. A resposta anterior muda qual é a melhor próxima pergunta.
2. **Responda você mesmo o que puder.** Antes de perguntar, verifique se a resposta já existe no código, nos documentos do projeto, no CLAUDE.md ou no histórico. Só pergunte o que você genuinamente não consegue descobrir sozinho. Perguntar o óbvio queima a paciência do usuário.
3. **Comece pelo que é caro errar.** Priorize perguntas sobre decisões difíceis de reverter (modelo de dados, fronteiras de sistema, quem paga o quê) sobre detalhes cosméticos.
4. **Vá fundo em respostas vagas.** Se o usuário responde "o sistema notifica o responsável", pergunte: quem exatamente é "o responsável"? Notifica como? E se ele não responder? Vagueza na entrevista vira bug em produção.

## Captura de vocabulário (em tempo real, não em lote)

Assim que um termo do domínio ficar claro na conversa, registre-o IMEDIATAMENTE em `CONTEXT.md` na raiz do projeto — não espere o fim da entrevista. Use a linguagem do próprio projeto/usuário, não sinônimos seus.

Formato do `CONTEXT.md`:

```markdown
# Contexto do projeto

## Glossário
| Termo | Definição | Fonte |
|---|---|---|
| <termo exato usado pelo usuário> | <definição na linguagem do projeto> | <conversa/documento> |

## Perguntas abertas
- [ ] <pergunta ainda não respondida>

## Decisões (ver docs/adr/ para as importantes)
- <decisões menores, uma linha cada>
```

Se `CONTEXT.md` já existe, atualize-o — nunca duplique termos; refine a definição existente.

## ADRs — apenas para decisões excepcionais

Crie um ADR em `docs/adr/NNNN-titulo-kebab.md` SOMENTE quando a decisão for:
- **difícil de reverter** (migração cara, contrato, arquitetura), OU
- **surpreendente vista isolada** (alguém no futuro perguntaria "por que diabos fizeram assim?"), OU
- **um trade-off genuíno** (havia alternativas boas e escolhemos uma com custo consciente).

ADR rotineiro é ruído. Formato:

```markdown
# NNNN. <Título da decisão>
Data: <data> | Status: aceita

## Contexto
<a situação que forçou a decisão>

## Decisão
<o que foi decidido, em uma frase direta>

## Consequências
<o que ganhamos, o que perdemos, o que fica travado>
```

## Quando parar

Pare de entrevistar quando: os termos centrais têm definição registrada, as decisões caras têm ADR, e você conseguiria escrever uma especificação sem precisar perguntar mais nada. Encerre com um resumo: termos capturados, ADRs criados, perguntas que ficaram abertas.

## O que esta skill NÃO é

- Não é para quando a direção já está clara — aí vá direto especificar/implementar.
- Não gere código nem especificação durante a entrevista. O entregável é entendimento documentado; a especificação vem depois, em outra etapa, usando o CONTEXT.md como base.
