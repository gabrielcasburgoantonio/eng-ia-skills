# 13-ai-engineering — Método Sandeco de Engenharia de Software com Agentes

Skills próprias (autor: toni) destiladas do curso **"Engenharia de Software com Agentes Inteligentes"** do Prof. Sandeco Macedo — 14 videoaulas transcritas + livro homônimo final (165p).

Tese central: **IA + processo estruturado = software sustentável** (o anti vibe-coding).

## As 8 skills

| Skill | Papel | Fase |
|---|---|---|
| **`eng-ia`** | Skill-índice. Não gera artefato — roteia qual skill usar em cada fase. | transversal |
| **`eng-ia-agent-harness`** | Desenha/audita o Agent Harness: mecanismos que tocam o modelo e tornam a execução confiável. Inclui DevContainer + YOLO (aula 13). | -1 |
| **`eng-ia-frameworks`** | Escolhe/opera um framework SDD sobre o harness: BMAD / SpecKit / Reversa. Injeta TDD. Aula 11. | 1 |
| **`eng-ia-bootstrap`** | Monta o Agent Harness do projeto: CLAUDE.md com regras de engenharia, `specs/`, `.eng-ia/`. | 0 |
| **`eng-ia-loop`** | Projeta loops de agente seguros (meta, verificação, memória, fuga). Aula 12. | transversal |
| **`eng-ia-micro-decisoes`** | Ledger de atritos/acordos humano-IA (4 linhas, ~30 tokens). Novidade da aula 08. | transversal |
| **`eng-ia-quality-gate`** | Portão pré-commit. Agrega `code-review` + `code-philosophy` + checklist Sandeco + pre-commit/secret-scan. | 4 |
| **`eng-ia-deploy`** | Deploy seguro: Docker → VPS → CI/CD, pre-commit/secret-scan, blindagem de custo/portas. Aula 14. | 5 |

## Por que essas (e não mais)

Antes de criar, auditei o skills-hub. O que já existia e foi **reusado em vez de duplicado**:

- **Spec (SDD)** — já coberto por `04-engenharia-reversa/reversa/agents/reversa-spec-sdd` e `07-arquitetura-produto/sdd-spec`. Não recriei.
- **Quebra em tarefas** — `reversa-to-do`. Não recriei.
- **Revisão de código** — `02-coding-quality/code-review` (4 camadas + gravidade). Reusado pelo quality-gate.
- **Filosofia de código** — `02-coding-quality/code-philosophy` (5 Leis). Reusado.

As 7 skills aqui preenchem só as **lacunas reais** do método Sandeco que nenhuma skill existente cobria: desenho/auditoria de Agent Harness (incl. DevContainer/YOLO), escolha/operação de framework SDD (BMAD/SpecKit/Reversa), bootstrap de projeto, engenharia de looping, micro-decisões como metodologia, e o portão de qualidade com os critérios de manutenibilidade do Sandeco (POO, coesão/acoplamento, token=custo, cross-reference spec↔código). Spec/tarefas/revisão continuam reusadas de skills existentes (`reversa-spec-sdd`, `reversa-to-do`, `code-review`, `code-philosophy`).

## Pipeline completo

```
/eng-ia-agent-harness -> desenha/audita o harness mínimo (contexto, memória, loop, ferramentas, verificação, guardrails; DevContainer+YOLO)
/eng-ia-bootstrap     -> monta o harness do projeto (CLAUDE.md, specs/, tests/, .eng-ia/)
/eng-ia-frameworks    -> escolhe/opera BMAD/SpecKit/Reversa (alternativa à spec manual)
/reversa-spec-sdd     -> spec da feature (múltiplos docs coesos)   (skill existente)
/reversa-to-do        -> tarefas atômicas                          (skill existente)
TDD                   -> teste antes do código (Red/Green/Refactor) — regra global no CLAUDE.md
code-philosophy       -> 5 Leis ao escrever                        (skill existente)
/eng-ia-loop          -> loops seguros (meta/verificação/memória/fuga; /go, reflection, Ralph)
/eng-ia-quality-gate  -> portão antes do commit (Q10/Q11 de TDD e regressão; pre-commit/secret-scan)
/eng-ia-micro-decisoes -> registra atritos (a qualquer momento)
/eng-ia-deploy        -> fase 5: Docker -> VPS -> CI/CD, seguro (aula 14)
```

Fonte: transcrições em `Engenharia de IA - SANDECO/transcricoes/aula-01..14.txt` + notas em `notas/`. As novidades (micro-decisões, skill-vs-hook, token-como-custo) concentram-se na **aula 08**. A **aula 09** aprofunda SDD e introduz **TDD com a pirâmide de 5 camadas**. A **aula 10** refina Agent Harness (guardrail ⊂ harness). A **aula 11** traz os frameworks **BMAD/SpecKit/Reversa** (→ `eng-ia-frameworks`; TDD não vem por padrão, injeta-se na Constituição). A **aula 12** traz a **engenharia de looping** (→ `eng-ia-loop`). A **aula 13** fecha o livro (cap. 9) com **DevContainer + YOLO** (execução autônoma segura). A **aula 14** (extra, convidado) cobre **deploy em VPS** (AWS LightSail, Docker, Git/CI-CD, pre-commit) — DevOps que alimenta a fase 5.
