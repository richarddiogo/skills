---
name: build-loop
description: Projetar e construir um loop de IA autônomo (objetivo + execução + verificação objetiva + iteração) no lugar de prompts manuais repetidos. Use quando o usuário quiser automatizar uma tarefa recorrente, transformar um prompt que ele roda toda hora em algo autônomo, ou pedir /build-loop.
---

# Build Loop — de prompt manual para loop autônomo

Um prompt é uma instrução estática. Um loop é um objetivo que a IA persegue até atingir — planejar, executar, verificar, realimentar, repetir — sem ninguém na cadeira. Esta skill guia a construção de um loop que funciona de verdade, em vez de um que só queima tokens.

## Passo 0 — Qualificar: a tarefa merece um loop?

Antes de construir qualquer coisa, verifique os 4 pré-requisitos. Se QUALQUER um faltar, diga ao usuário que um loop não se justifica ainda e o que falta:

1. **Frequência**: a tarefa se repete pelo menos semanalmente? Se não, o custo de montar o loop nunca se paga.
2. **Detecção objetiva de falha**: existe teste, compilação, lint, validação de schema ou checagem programática que diz PASSOU/FALHOU sem opinião? Sem barreira objetiva não existe loop — existe um agente se autoavaliando com generosidade.
3. **Orçamento de tokens**: o usuário aceita o desperdício computacional de reler contexto e tentar de novo?
4. **Ferramentas**: o agente consegue executar, ver logs e debugar o próprio trabalho no ambiente?

## Passo 1 — Torne UMA execução manual confiável

Não automatize o que ainda não funciona à mão. Rode a tarefa uma vez, manualmente, até o resultado ser aceito. Anote cada decisão, comando e critério usado.

## Passo 2 — Destile em conhecimento persistente

Transforme o que aprendeu no Passo 1 em um arquivo de skill/CLAUDE.md do projeto: contexto, convenções, o que já foi tentado e falhou. É a memória institucional que impede o loop de recomeçar do zero a cada rodada.

## Passo 3 — Monte os 5 blocos

Todo loop funcional tem estes blocos — construa cada um explicitamente:

| Bloco | Função | Sem ele |
|---|---|---|
| **Verificação** (o coração) | Teste objetivo que decide sucesso/falha | O loop só gasta dinheiro |
| **Estado** | Arquivo/board registrando o que foi tentado e falhou | Cada rodada recomeça do zero |
| **Separação criador/verificador** | Quem escreveu não avalia o próprio trabalho — use subagente revisor | Autoavaliação generosa, "pronto!" em trabalho pela metade |
| **Condição de parada** | DUAS saídas: objetivo atingido OU máximo de tentativas | Loop infinito queimando tokens |
| **Automação (heartbeat)** | Agendamento (cron/schedule) ou gatilho por evento | É um script que você rodou uma vez e esqueceu |

Opcional mas valioso: **conectores** — o loop abre PR, fecha ticket, notifica canal; senão o resultado morre num terminal.

## Passo 4 — Escreva o loop

Estruture como: `DESCOBRIR → PLANEJAR → EXECUTAR → VERIFICAR → ITERAR`, com o estado gravado em arquivo (ex.: `loop-state.md`) entre rodadas. A verificação NUNCA é "o agente acha que ficou bom" — é um comando com exit code.

## Passo 5 — Só então agende

Depois que uma rodada completa do loop funcionou supervisionada, agende (cron, /schedule, CI). Nunca agende um loop que nunca completou uma rodada com sucesso.

## Métrica de sucesso e armadilhas

- **Métrica**: custo por mudança ACEITA. Se menos de ~50% do que o loop produz é aceito, você está fazendo o trabalho de revisão que o loop deveria eliminar — desligue e melhore a barreira.
- **Loop Ralph Wiggum**: o agente declara vitória em trabalho pela metade. Remédio: barreira mais burra (teste objetivo), não agente mais esperto.
- **Dívida cognitiva**: quanto mais código o loop entrega sem você ler, maior o buraco entre o que o repositório contém e o que você entende. Reserve atenção humana para os 5% de risco real; o loop faz os 95% chatos.
- **Comece com UM loop**, o da tarefa recorrente mais irritante. Não dez.
