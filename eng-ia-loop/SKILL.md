---
name: eng-ia-loop
description: "Projeta loops de agente seguros (engenharia de looping): meta, gatilho, verificacao, memoria e fuga. Use quando o usuario quiser automatizar uma tarefa iterativa (escreve-avalia-melhora), rodar algo ate atingir um score/criterio, montar um /go ou /loop no Cloud Code, usar reflection, ou evitar gasto descontrolado de token em loop. Nao use loop quando nao houver aferidor de saida."
license: MIT
metadata:
  author: toni
  version: 1.0.0
  framework: eng-ia
  role: loop-designer
  source: Curso 'Engenharia de Software com Agentes Inteligentes' (Prof. Sandeco Macedo), aula 12
---

# eng-ia-loop

Engenharia de looping: entregar ao harness uma **meta + start + insumos** e deixá-lo se **auto-corrigir** até atingir a meta, sem você dando `continue`. Um loop é um **prompt circular** — a engenharia de prompt não morreu.

> **Regra zero: se não há aferidor de saída, não é caso de loop.** Igual `for`/`while true` precisa de `break`.

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

Níveis de verificação, do mais forte ao mais importante: **determinístico → LLM-juiz → checkpoint humano**.

## Padrões

- **Reflection**: um agente produz, outro critica (contextos separados). `/go` já é reflection embutido. Loop = reflection **com critério de saída**.
- **Loop de Ralph**: a cada volta limpa o contexto (contexto fresco), vence por insistência, pontua contra a **mesma marca fixa** para comparabilidade.
- **Loop de loops**: um índice com um `/go` por etapa → loops aninhados. Trate loops como **funções reutilizáveis**.
- **Estados de fim**: `sucesso | bloqueado | esgotado-com-erro`. **Erro nunca é sucesso.**

## Cloud Code nativo

- **`/go`**: loop de meta (pede condição + objetivo; abre subagentes produz+avalia; pode chamar outro harness como juiz).
- **`/loop`**: scheduler (executa ação de tempos em tempos).
- Loop que só dispara comandos de terminal consome **pouco** token, mesmo rodando muito tempo.

## Saída

Gere um `<nome>-loop.md` (padrão Loop Library) com: front-matter, gatilho, objetivo, **entradas**, **meta**, **verificação/check**, **memória de saída**. Depois: `/go /<nome>` + parâmetros. Guarde em `loops/` (6ª categoria de artefato, ao lado de skills/commands).

## Como combinar

- `eng-ia-agent-harness`: loop/verificação/memória são componentes de harness — aqui detalhados. Para autonomia pesada, rode o loop em DevContainer + YOLO.
- `eng-ia-quality-gate`: o verificador-crítico e o checkpoint humano são gates.
- `cinturao-regressao`: o verificador ecoa o cinturão.
- `eng-ia-frameworks`: use loop para orquestrar stories/tasks de BMAD/SpecKit.

## Origem pedagógica

Aula 12 do curso Engenharia de Software com Agentes Inteligentes, Prof. Sandeco Macedo. Origem do hype: Peter Steinberg e Boris (têm token ilimitado — você não). Base: Loop Library / Matthew Berman.
