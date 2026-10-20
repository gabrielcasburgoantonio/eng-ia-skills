---
name: eng-ia-loop
description: "Projeta loops de agente seguros (engenharia de looping): meta, gatilho, verificacao, memoria e fuga. Use quando o usuario quiser automatizar uma tarefa iterativa (escreve-avalia-melhora), rodar algo ate atingir um score/criterio, montar um /go ou /loop no Cloud Code, usar reflection, ou evitar gasto descontrolado de token em loop. Nao use loop quando nao houver aferidor de saida."
license: MIT
metadata:
  author: toni
  version: 1.1.0
  framework: eng-ia
  role: loop-designer
  source: Curso 'Engenharia de Software com Agentes Inteligentes' (Prof. Sandeco Macedo), aula 12 + deltas da skill sandeco-loop (sandeco/prompts @ 3bf5a6a, 2026-06-26, sem arquivo de licença — reescrita, não copiada)
---

# eng-ia-loop

Engenharia de looping: entregar ao harness uma **meta + start + insumos** e deixá-lo se **auto-corrigir** até atingir a meta, sem você dando `continue`. Um loop é um **prompt circular** — a engenharia de prompt não morreu.

> **Regra zero: se não há aferidor de saída, não é caso de loop.** Igual `for`/`while true` precisa de `break`.
>
> **Triagem:** pergunte "o resultado de cada volta muda a próxima ação?". Se não muda, não é loop, é **prompt agendado** — entregue a tarefa + a cadência e pare.

## As 4 fases (todo loop tem)

`Gatilho → Execução → Verificação → Registro`

- **Gatilho**: manual / agendado (scheduler) / por evento.
- **Execução**: percebe → raciocina → age, a cada volta.
- **Verificação**: o **coração** do loop. Sem checagem de saída, não faça.
- **Registro/memória**: o loop **tem que produzir e salvar** algo (MD/JSON/DB) para comparar entre voltas.

## Checklist de projeto (obrigatório antes de rodar)

1. Existe **aferidor / critério de saída**?
2. Meta é **verificável** (número/regra/verdade de campo) ou **subjetiva** (LLM-juiz)? Se subjetiva → use um **2º LLM como juiz** (a LLM que executa se auto-favorece).
3. O **verificador foi testado** (acha erro antes de você confiar — como o RED do TDD)?
4. Há **fuga / stop-early** (máx. de voltas)? Ex.: "atinja 9/10, mas no máximo 4 versões". Senão gasta token infinito.
5. Onde está a **memória de saída** (histórico p/ comparar)?
6. Há **rédea / checkpoint humano** nos passos de risco (destruição de artefatos)?
7. A **evidência do check aparece na conversa**? O avaliador do `/goal` é um modelo rápido que **só lê o transcript** — não roda comando nem abre arquivo. A volta tem que rodar o check e colar a saída; a condição de parada é escrita sobre essa saída.
8. Está medida a **saúde**: custo por mudança aceita = tokens (ou R$) ÷ mudanças que sobreviveram ao check. Loop que queima sem mudança aceita está quebrado.

Níveis de verificação, do mais forte ao mais importante: **determinístico → LLM-juiz → checkpoint humano**.

## Endurecimento (contra reward hacking e autoengano)

- **Nunca auto-score.** Modelo que se dá nota de 1 a 10 infla a própria nota; sem feedback externo, a autocorreção estagna ou piora. A parada vem de um check que roda **fora** da cabeça do agente.
- **Juiz-LLM endurecido** (quando a meta é subjetiva): rubrica **congelada**; quem gera ≠ quem julga (sessão/modelo diferente); e **quebre a simetria de contexto** — o juiz não recebe o histórico de quem fez. Compartilhar o histórico é o que dispara o reward hacking.
- **Estados de parada nomeados:** `sucesso | sem-progresso (ex.: 2 voltas sem ganho) | bloqueado | esgotado (teto de turnos/custo)`. Erro ou teto estourado **nunca** é sucesso.
- **Disciplina da volta:** fotografe o "antes" (linha de base), ataque o **pior primeiro**, faça **uma** mudança, rode o check, mantenha só se nada regrediu — senão reverta.
- **Loop compõe skills nomeadas.** Loop sem skill reusável dentro é um `while true` em volta de um estranho.

## Padrões

- **Reflection**: um agente produz, outro critica (contextos separados). `/go` já é reflection embutido. Loop = reflection **com critério de saída**.
- **Loop de Ralph**: a cada volta limpa o contexto (contexto fresco), vence por insistência, pontua contra a **mesma marca fixa** para comparabilidade.
- **Loop de loops (sub-loops)**: um índice com um `/goal` por etapa → loops aninhados, como `for` dentro de `for`. Trate loops como **funções reutilizáveis**: o sub-loop é citado pelo **nome do documento** (`<sub>-loop.md`, na mesma pasta) e roda até a parada **dele** antes do pai seguir. O teto é **multiplicativo** (voltas do pai × voltas do filho) e **ciclo é proibido** (sub-loop nunca chama, direta ou indiretamente, quem o chamou).

## Claude Code nativo

- **`/goal`** (na transcrição da aula aparece `/go`; a `sandeco-loop` e o Reversa usam `/goal`): loop de meta (pede condição + objetivo; abre subagentes produz+avalia; pode chamar outro harness como juiz).
- **`/loop`**: scheduler (executa ação de tempos em tempos).
- **Cabe numa janela de contexto?** Aciona com `/goal`. **Estoura o contexto** (muitas voltas; a qualidade cai além de ~100-150k tokens)? Use **esqueleto Ralph**: laço de shell que relê o documento + o estado no disco a cada volta, com contexto fresco (exige sandbox e teto de custo).
- Loop que só dispara comandos de terminal consome **pouco** token, mesmo rodando muito tempo.

## Saída

Gere um `<nome>-loop.md` (padrão Loop Library) — **a skill escreve a especificação, não roda o loop.** Gabarito completo, seção por seção, em [`references/gabarito-loop.md`](references/gabarito-loop.md): descrição, use-quando, entradas (única seção opcional), meta, check, passos da volta, estados de parada, guardrails, memória, sub-loops, por que funciona, como acionar, métrica de saúde. Seção que não se aplica leva "não se aplica" + motivo (silêncio esconde furo).

**Antes de escrever, pergunte onde salvar:** `./loops/` (específico do projeto) ou `~/.claude/loops/` (chamável de qualquer projeto) — `loops/` é a 6ª categoria de artefato, ao lado de skills/commands. E se vira comando `/loop-<nome>` (em `./.claude/commands/` ou `~/.claude/commands/`, conforme o local), acionável sozinho ou como `/goal /loop-<nome>`.

## Como combinar

- `eng-ia-agent-harness`: loop/verificação/memória são componentes de harness — aqui detalhados. Para autonomia pesada, rode o loop em DevContainer + YOLO.
- `eng-ia-quality-gate`: o verificador-crítico e o checkpoint humano são gates.
- `cinturao-regressao`: o verificador ecoa o cinturão.
- `eng-ia-frameworks`: use loop para orquestrar stories/tasks de BMAD/SpecKit.

## Origem pedagógica

Aula 12 do curso Engenharia de Software com Agentes Inteligentes, Prof. Sandeco Macedo. Origem do hype: Peter Steinberg e Boris (têm token ilimitado — você não). Base: Loop Library / Matthew Berman.

**v1.1.0 (2026-10-19):** incorporados os deltas da skill `sandeco-loop` (`sandeco/prompts` @ `3bf5a6a`) — triagem prompt-agendado, evidência no transcript, juiz com simetria quebrada, sub-loops com teto multiplicativo, métrica de saúde, gabarito do documento e comando `/loop-<nome>`. Decisão: **não** virou skill separada (mesma aula, mesmo domínio — duplicata).
