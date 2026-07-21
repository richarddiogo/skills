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

## Créditos das técnicas

- **grill-with-docs** — baseada em https://www.aihero.dev/grill-with-docs
- **build-loop** — baseada em https://youmind.com/pt-BR/landing/x-viral-articles/build-ai-loops-replace-prompts
