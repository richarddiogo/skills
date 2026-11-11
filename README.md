# Claude Skills

Coleção de *skills* agnósticas para o Claude Code — funcionam em **qualquer projeto**, em qualquer linguagem. Cada skill é uma técnica reutilizável, não amarrada a nenhum código específico.

## Skills

| Skill | O que faz | Invoque com |
|---|---|---|
| [`grill-with-docs`](grill-with-docs/) | Entrevista intensiva antes de construir — uma pergunta por vez, capturando o vocabulário do domínio em `CONTEXT.md` e decisões difíceis em ADRs. | `/grill-with-docs` |
| [`build-loop`](build-loop/) | Projetar um loop de IA autônomo (objetivo + execução + verificação objetiva + iteração) no lugar de prompts manuais repetidos. | `/build-loop` |
| [`smoke-anchor`](smoke-anchor/) | Provar que algo que você acabou de construir funciona — plantar uma âncora objetiva (oráculo ou marco) e martelar até verde, com parada dura. | `/smoke-anchor` |

## Como instalar em outra máquina / outro projeto

As skills do Claude Code vivem em `~/.claude/skills/`. Para usar estas em qualquer projeto:

```bash
# clona este repo
git clone https://github.com/<seu-usuario>/skills.git ~/tmp-skills

# copia cada skill para a pasta global de skills do Claude Code
mkdir -p ~/.claude/skills
cp -R ~/tmp-skills/grill-with-docs ~/.claude/skills/
cp -R ~/tmp-skills/build-loop     ~/.claude/skills/
```

Ou, se preferir manter versionado, clone direto dentro de `~/.claude/skills/`:

```bash
cd ~/.claude/skills
git clone https://github.com/<seu-usuario>/skills.git .
```

Depois é só abrir o Claude Code em qualquer projeto e chamar `/grill-with-docs` ou `/build-loop`.

## Estrutura

Cada skill é uma pasta com um `SKILL.md` que tem um cabeçalho YAML (`name` + `description`) e as instruções. Para criar novas, siga o mesmo formato — o `description` é o que o Claude usa para decidir quando a skill é relevante.


Perguntas de OLLAMA LOCAL
======================================================================
ETAPA 1 — IDENTIFICAR SISTEMA E HARDWARE
======================================================================

Detecte automaticamente se o ambiente é Windows, macOS, Linux ou WSL.

Colete, quando disponível:

* sistema operacional;
* edição e versão;
* arquitetura: x86_64, ARM64 etc.;
* hostname sanitizado;
* shell atual;
* CPU;
* quantidade de núcleos;
* memória RAM ou memória unificada;
* GPU disponível;
* driver ou runtime GPU;
* espaço livre em disco;
* diretório temporário;
* usuário sem expor domínio completo quando sensível;
* existência de WSL;
* existência de virtualização;
* variáveis PATH relevantes.
======================================================================
ETAPA 3 — AUDITORIA COMPLETA DO OLLAMA
======================================================================

Verifique:

* se o executável ollama existe;
* caminho do executável;
* versão;
* se o servidor está ativo;
* URL/base URL utilizada;
* porta;
* processo;
* consumo aproximado;
* modelos instalados;
* modelos carregados;
* tamanho de cada modelo;
* família;
* quantidade de parâmetros;
* quantização;
* formato;
* template;
* parâmetros do Modelfile;
* contexto nominal;
* contexto configurado;
* contexto efetivamente alocado;
* capabilities informadas pela API;
* data de modificação.

Execute, conforme o sistema operacional e disponibilidade:

* ollama --version
* ollama ls
* ollama ps
* ollama show <modelo>
* ollama show --modelfile <modelo>
* GET http://localhost:11434/api/version
* GET http://localhost:11434/api/tags
* POST http://localhost:11434/api/show para cada modelo encontrado

Não assuma que o nome do modelo revela sua capacidade.
Use os metadados reais.

Para cada modelo encontrado, crie uma ficha:

* nome completo;
* digest;
* família;
* parameter_size;
* quantization_level;
* tamanho em disco;
* context_length nominal;
* num_ctx configurado;
* capabilities;
* chat disponível;
* generate disponível;
* embeddings disponível;
* JSON estruturado;
* tools/function calling;
* velocidade aproximada;
* memória aproximada;
* utilização recomendada;
* limitações.
======================================================================
ETAPA 4 — TESTES CONTROLADOS DOS MODELOS
======================================================================

Realize testes mínimos, sem conteúdo corporativo.

Use temperatura zero ou a menor configuração disponível.

TESTE A — CHAT BÁSICO

Envie:

"Responda apenas com a palavra OK."

Verifique:

* status HTTP;
* latência;
* tokens de entrada;
* tokens de saída;
* resposta válida.

TESTE B — JSON ESTRUTURADO

Solicite exatamente:

Retorne somente JSON válido no formato:
{
  "intent": "classificacao",
  "risk": "baixo",
  "requires_external_model": false
}

Valide o JSON com parser real.
Não considere visualmente válido sem fazer parse.

TESTE C — CONTEXTO

Não tente preencher toda a janela.
Use metadados para identificar a janela declarada.

Faça apenas um teste curto suficiente para verificar:

* se num_ctx pode ser informado;
* se o servidor aceita a opção;
* contexto efetivo mostrado por ollama ps, quando possível.

Não faça teste de 128K nem consuma memória excessiva.

TESTE D — EMBEDDINGS

Para cada modelo instalado, tente o endpoint:

POST /api/embed

Entrada:

"Adicionar paginação ao endpoint de pedidos"

Registre:

* sucesso ou erro;
* dimensão do vetor;
* tempo;
* se o vetor contém valores numéricos;
* se duas frases iguais produzem embeddings consistentes;
* se frases semanticamente próximas parecem mais próximas que frases
  totalmente diferentes.

Use três textos de teste:

1. "Adicionar paginação ao endpoint de pedidos."
2. "Limitar e dividir em páginas os resultados da API de pedidos."
3. "A previsão do tempo indica chuva amanhã."

Calcule similaridade de cosseno somente se houver embeddings válidos.

Não declare o modelo adequado para dense retrieval apenas porque retornou
um vetor. Classifique separadamente:

* endpoint funciona;
* modelo é dedicado a embeddings;
* qualidade preliminar parece utilizável;
* qualidade precisa de benchmark.

TESTE E — TOOL CALLING

Somente execute se:

* a API informar capability de tools;
* ou a documentação/template local indicar suporte.

Use uma ferramenta falsa e inofensiva:

get_test_value(value: string)

Peça ao modelo para chamar a ferramenta com value="ok".

Registre:

* tool call estruturado;
* texto fingindo chamar ferramenta;
* ausência de suporte;
* resultado inconclusivo.

/// teste
Construa um serviço serverless na AWS que permite a um dev logado via SSO Azure
(Entra ID) autorizar, sob demanda, validações somente leitura em uma conta AWS,
usando o device authorization flow do IAM Identity Center. Nenhum token de acesso
AWS pode ser persistido.

## Parâmetros do projeto
- Runtime das Lambdas: [ex.: Python 3.13 | Node 22]
- IaC: [ex.: Terraform | CDK | SAM]
- Frontend: [ex.: React já existente | criar página simples]
- Validações a executar na conta alvo: [listar as checagens, todas read-only]

## Arquitetura
- Frontend autenticado com SSO Azure (MSAL), enviando o access token no header
  Authorization.
- API Gateway HTTP API com JWT authorizer validando issuer, audience e assinatura
  do Entra ID.
- Lambda `start` (POST /start), Lambda `status` (GET /status/{requestId}) e
  Lambda `worker` (invocação assíncrona).
- DynamoDB: tabela `device_flows` (PK = "{tid}#{oid}", SK = requestId) com
  atributo de TTL.
- KMS: uma chave dedicada para cifrar o deviceCode.
- Secrets Manager: clientId/clientSecret do registro OIDC (sso-oidc:RegisterClient),
  com renovação antes de expirar.
- Configuração por tenant no servidor (tid -> startUrl, região do Identity Center,
  contas e permission sets permitidos). Nunca aceitar startUrl vindo do front.

## Fluxo
1. POST /start {accountId, roleName}
   - Extrair tid e oid das claims do JWT já validado pelo authorizer.
   - Validar accountId/roleName contra a allowlist do tenant.
   - Chamar sso-oidc:StartDeviceAuthorization.
   - Cifrar o deviceCode com KMS usando encryption context {tid, oid, requestId}.
   - Gravar item: deviceCode cifrado, accountId, roleName, interval, expiresAt,
     status=PENDING, ttl.
   - Responder {requestId, verificationUriComplete, userCode, interval, expiresIn}.
2. GET /status/{requestId}
   - Ler o item usando PK montada a partir do JWT (nunca de parâmetro).
   - Se expiresAt já passou: apagar o item e responder EXPIRED.
   - Se status=PENDING: decifrar o deviceCode e chamar sso-oidc:CreateToken.
     - AuthorizationPendingException -> responder PENDING.
     - SlowDownException -> responder PENDING com interval maior.
     - ExpiredTokenException / AccessDeniedException -> apagar item, responder
       EXPIRED ou DENIED.
     - Sucesso -> marcar status=RUNNING com escrita condicional
       (status = PENDING), remover o deviceCode do item e invocar a Lambda
       `worker` de forma assíncrona passando o accessToken apenas no payload
       da invocação.
   - Se status=RUNNING: responder RUNNING.
   - Se status=DONE ou FAILED: responder com o resultado e apagar o item.
3. Lambda worker
   - Chamar sso:GetRoleCredentials(accountId, roleName, accessToken).
   - Criar clients AWS explicitamente com essas credenciais temporárias.
   - Executar as validações e gravar apenas o resultado no item
     (status=DONE ou FAILED). Nunca gravar token ou credencial.
4. Frontend
   - Mostrar o link e o userCode, abrir o link em nova aba, consultar /status
     respeitando o interval e exibir o resultado ou o erro.

## Regras de segurança obrigatórias
- Usar somente SDK. Proibido AWS CLI, ~/.aws, perfis ou cadeia default de
  credenciais para acessar a conta alvo.
- Nenhum token, deviceCode ou credencial em variável global, /tmp, log,
  mensagem de erro ou resposta HTTP. Apenas variáveis locais do handler.
- A identidade do usuário vem exclusivamente das claims do JWT validado.
- Roles das Lambdas com privilégio mínimo: só `start` tem kms:Encrypt; só
  `status` tem kms:Decrypt; só `status` invoca `worker`; nenhuma role tem
  permissão direta nas contas alvo.
- Não confiar no TTL do DynamoDB para expiração: conferir expiresAt no código
  e apagar itens explicitamente.
- CloudTrail data events habilitados para a tabela e para a chave KMS.
- Rate limit no /start por usuário e throttling no API Gateway.
- A invocação assíncrona do worker não pode ter DLQ nem destino de falha que
  persista o payload; configurar retry = 0.

## Entregáveis
- Código das três Lambdas, IaC completo, e a parte do frontend.
- Testes unitários cobrindo: isolamento entre usuários (usuário B não lê fluxo
  do usuário A), cada exceção do CreateToken, expiração, dupla chamada
  concorrente ao /status (só uma dispara o worker) e ausência de segredos em logs.
- README com o desenho, as permissões IAM e como registrar um novo tenant.

Antes de codar, apresente o plano e aponte qualquer ponto em que estas regras
entrem em conflito com as validações pedidas.

Não confunda uma resposta em texto com function calling real.
## Créditos das técnicas

- **grill-with-docs** — baseada em https://www.aihero.dev/grill-with-docs
- **build-loop** — baseada em https://youmind.com/pt-BR/landing/x-viral-articles/build-ai-loops-replace-prompts
