# 13-ai-engineering — Método Sandeco de Engenharia de Software com Agentes

Skills próprias (autor: toni) destiladas do curso **"Engenharia de Software com Agentes Inteligentes"** do Prof. Sandeco Macedo — 9 videoaulas transcritas + livro homônimo.

Tese central: **IA + processo estruturado = software sustentável** (o anti vibe-coding).

## As 4 skills

| Skill | Papel | Fase |
|---|---|---|
| **`eng-ia`** | Skill-índice. Não gera artefato — roteia qual skill usar em cada fase. | transversal |
| **`eng-ia-bootstrap`** | Monta o Agent Harness do projeto: CLAUDE.md com regras de engenharia, `specs/`, `.eng-ia/`. | 0 |
| **`eng-ia-micro-decisoes`** | Ledger de atritos/acordos humano-IA (4 linhas, ~30 tokens). Novidade da aula 08. | transversal |
| **`eng-ia-quality-gate`** | Portão pré-commit. Agrega `code-review` + `code-philosophy` + checklist Sandeco. | 4 |

## Por que só 4 (e não mais)

Antes de criar, auditei o skills-hub. O que já existia e foi **reusado em vez de duplicado**:

- **Spec (SDD)** → já coberto por `04-engenharia-reversa/reversa/agents/reversa-spec-sdd` e `07-arquitetura-produto/sdd-spec`. Não recriei.
- **Quebra em tarefas** → `reversa-to-do`. Não recriei.
- **Revisão de código** → `02-coding-quality/code-review` (4 camadas + gravidade). Reusado pelo quality-gate.
- **Filosofia de código** → `02-coding-quality/code-philosophy` (5 Leis). Reusado.

As 4 skills aqui preenchem só as **lacunas reais** do método Sandeco que nenhuma skill existente cobria: bootstrap de harness, micro-decisões como metodologia, e o portão de qualidade com os critérios de manutenibilidade do Sandeco (POO, coesão/acoplamento, token=custo, cross-reference spec↔código).

## Pipeline completo

```
/eng-ia-bootstrap   →  monta o harness (CLAUDE.md, specs/, tests/, .eng-ia/)
/reversa-spec-sdd   →  spec da feature (múltiplos docs coesos)   (skill existente)
/reversa-to-do      →  tarefas atômicas                          (skill existente)
TDD                 →  teste antes do código (Red/Green/Refactor) — regra global no CLAUDE.md
code-philosophy     →  5 Leis ao escrever                        (skill existente)
/eng-ia-quality-gate →  portão antes do commit (inclui Q10/Q11 de TDD e regressão)
/eng-ia-micro-decisoes → registra atritos (a qualquer momento)
```

Fonte: transcrições em `Engenharia de IA - SANDECO/transcricoes/aula-01..09.txt`. As novidades (micro-decisões, skill-vs-hook, token-como-custo) concentram-se na **aula 08**. A **aula 09** aprofunda SDD (múltiplos documentos, não-objetivos no PRD, specs além de software) e introduz **TDD com a pirâmide de 5 camadas** (unitário, integração, contrato, end-to-end, regressão).
