---
name: eng-ia-frameworks
description: "Escolhe e opera um framework SDD sobre o Agent Harness: BMAD, SpecKit ou Reversa/Reversa-Anil. Use quando o usuario for comecar um projeto do zero, tiver codigo legado para especificar, perguntar qual framework usar (BMAD x SpecKit x Reversa), ou quiser injetar TDD/regras num desses frameworks. Nao substitui a spec manual (reversa-spec-sdd) — orquestra o framework que a gera."
license: MIT
metadata:
  author: toni
  version: 1.0.0
  framework: eng-ia
  role: framework-selector
  source: Curso 'Engenharia de Software com Agentes Inteligentes' (Prof. Sandeco Macedo), aula 11
---

# eng-ia-frameworks

Três frameworks de spec-driven development ficam **em cima do Agent Harness** e o guiam (não o substituem): **BMAD**, **SpecKit** e **Reversa**. Todos instalam local, aceitam qualquer harness (Claude Code, Codex, Antigravity, Gemini CLI) e funcionam por interação (`continue, continue`). Esta skill ajuda a **escolher** e a **plugar as regras certas** — sobretudo TDD, que nenhum traz por padrão.

## Escolha rápida

| Situação | Framework |
|---|---|
| Código **legado** → gerar spec do existente | **Reversa** (só lê o legado, nunca altera — diretiva no CLAUDE.md) |
| Greenfield, spec versionada, direto, **token-sensível** | **SpecKit** (do GitHub) ou **Reversa Anil** |
| Exploração ágil, brainstorm rico com personas | **BMAD** (mas é **verboso → gasta token**) |

Recomendação do Sandeco: **SpecKit ou Reversa > BMAD** (verbosidade). Não use dois no mesmo projeto — escolha um.

## Os frameworks

- **BMAD** — time ágil de personas (Scrum). Fluxo: **Brief (Analista/Mary) → PRD (PM) → Arquitetura → Stories (Scrum Master) → Código (Dev) → QA**. Cada story = 1 feature. Instala com `bmad method install` (Node); pastas `.bmad`/`bmad output`; `/bmad help` posiciona no fluxo. Trabalha com skills.
- **SpecKit** — esteira de specs versionada (SDD puro). Sequência: **Constituição → Specify → Plan → Task → Implementação**, com **gate entre etapas** (`clarify` em specify→plan; análise de coerência em task→impl). Instala via `uv` (`uv tool install specify-cli` → `specify init <projeto>`); pastas `.specify`/`specs` (por feature). Aceita **um** harness.
- **Reversa / Reversa Anil** — do Sandeco. Diferencial: **reverse** (spec de legado). **Reversa Anil** = equivalente ao SpecKit p/ greenfield. `github.com/sandeco/reversa`.

## Injetar TDD e regras (crítico)

Nenhum framework traz TDD por padrão. Três formas (use as três se quiser garantir):

1. Pedir explícito na codificação: *"crie em TDD"*.
2. **Na Constituição** (SpecKit) / regras do projeto: *"todo código, antes de implementado, tem teste no padrão TDD"*, além de POO, alta coesão, baixo acoplamento.
3. **Adicionar um agente/skill próprio** ao framework (eles instalam local, skills abertas): plugue `eng-ia-quality-gate` e `cinturao-regressao` como o "agente a mais" entre Task e Implementação.

A **Constituição** do SpecKit é o lugar canônico das leis do projeto — funciona como um CLAUDE.md do framework.

## Workflow

1. **Classifique**: legado? greenfield? token-sensível? → escolha pela tabela.
2. **Instale** o framework escolhido (comandos acima) sobre o harness.
3. **Configure as leis** (Constituição/regras): POO, coesão/acoplamento, **TDD**, cross-reference spec↔código.
4. **Rode em interação** (iterativo-incremental — fatie em partes pequenas; não gere tudo de uma vez, senão vira cascata).
5. **Injete os gates** (`eng-ia-quality-gate`, `cinturao-regressao`).
6. Para execução autônoma pesada, combine com `eng-ia-agent-harness` (DevContainer + YOLO).

## Como combinar

- `eng-ia` decide a fase; esta skill é a **fase 1 (Requisitos→Spec) quando você usa um framework** em vez da spec manual.
- `reversa-spec-sdd` / `sdd-spec` = a spec manual (subset dos 15 docs). Reversa Anil e SpecKit automatizam isso.
- `eng-ia-quality-gate` + `cinturao-regressao` = o TDD/gate que se pluga na Constituição.
- `eng-ia-loop` = para orquestrar as stories/tasks em loop autônomo.

## Origem pedagógica

Aula 11 do curso Engenharia de Software com Agentes Inteligentes, Prof. Sandeco Macedo (BMAD, SpecKit, Reversa; comparativo BMAD=cap.2/ágil, SpecKit=cap.6/SDD).
