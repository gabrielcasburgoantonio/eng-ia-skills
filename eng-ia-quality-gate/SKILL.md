---
name: eng-ia-quality-gate
description: 'Portão pré-commit do método Sandeco. Agregador: chama code-review + code-philosophy + checklist Q1-Q13 (POO, coesão, TDD, regressão, spec, git, segredos/pre-commit, loop seguro). Entrega veredito pass/fail. Acione com: /eng-ia-quality-gate, revisa antes de commitar, quality gate.'
license: MIT
metadata:
  author: toni
  version: 1.0.0
  framework: eng-ia
  role: quality-gate
  phase: 4-pre-commit
  source: Curso 'Engenharia de Software com Agentes Inteligentes' (Prof. Sandeco Macedo)
  compatibility: Claude Code, Codex, Cursor, Gemini CLI e demais agentes compatíveis com Agent Skills.
---

# eng-ia-quality-gate, portão antes de subir

Agregador. **Não reimplementa** revisão de código nem análise estática — delega para skills que já fazem isso bem e adiciona a camada de manutenibilidade do método Sandeco.

## O que ele orquestra

1. **`code-review`** — 4 camadas (corretude, segurança, performance, estilo) com classificação de gravidade (🔴🟠🟡🟢) e limite de confiança ≥80%.
2. **`code-philosophy`** — as 5 Leis da Defesa Elegante (Guard Clauses, Parse Don't Validate, Atomic Predictability, Fail Fast, Intentional Naming).
3. **Checklist Sandeco** (abaixo) — o que o método exige além de "o código funciona".

> **Executores opcionais (quando um achado precisa virar correção):** o quality-gate **aponta**, mas os agentes de qualidade do Reversa v1.3.2 **executam** a correção concreta — `reversa-refactor`, `reversa-optimize`, `reversa-prune`, `reversa-simplify`, `reversa-standardize`, `reversa-audit`. Bug recorrente vira teste permanente com `cinturao-regressao`.

## Checklist de manutenibilidade (critérios eng-ia)

Cada item é **pass/fail**. Falha não bloqueia automaticamente — vira um achado que o humano decide.

| # | Critério | Pergunta |
|---|---|---|
| Q1 | POO | A lógica está em classes de responsabilidade única, ou virou um amontoado procedural? |
| Q2 | Coesão | Cada arquivo faz uma coisa só? |
| Q3 | Acoplamento | Mudar um módulo força mudar vários outros? |
| Q4 | Token = custo | Os arquivos são pequenos o bastante para um LLM ler sem estourar contexto? Há arquivo-monstro? |
| Q5 | Padrão de projeto | Onde cabia um pattern conhecido, ele foi nomeado/usado — ou reinventado à mão? |
| Q6 | Spec | Existe spec em `specs/` para o que foi implementado? |
| Q7 | Cross-reference | O código aponta o ID da spec, e a spec aponta o arquivo? |
| Q8 | Git | As mudanças estão em commits pequenos e descritivos? |
| Q9 | Micro-decisão | Houve atrito/decisão de arquitetura nesta sessão que não foi registrado em `.eng-ia/micro-decisoes.md`? |
| Q10 | TDD | Existe teste para o código entregue, e ele foi escrito antes (ou pelo menos junto)? Cobre as camadas relevantes da pirâmide (unitário/integração/contrato/end-to-end)? |
| Q11 | Regressão | Bugs reportados nesta sessão (ou anteriores que tocaram este código) viraram caso de regressão em `tests/`? |
| Q12 | Segredos & pre-commit | Nenhuma chave/API/segredo no diff? Há `.pre-commit` com secret-scan + format (ruff) barrando o commit? Repo privado? (aula 14). **Se for app web, o diff limpo não basta** — a chave pode estar no *bundle* que vai pro navegador: `16-seguranca/segredos-no-front/` |
| Q13 | Loop seguro | Se há loop de agente (`/go`, reflection), ele tem aferidor de saída, fuga/stop-early e verificador testado? Meta subjetiva usa 2º LLM como juiz? (aula 12 — ver `eng-ia-loop`) |

## Procedimento

1. Determinar o escopo (diff staged, arquivos tocados na sessão, ou alvo que o usuário indicar).
2. Rodar `code-review` no escopo. Coletar achados com gravidade.
3. Rodar a aderência de `code-philosophy` (5 Leis).
4. Percorrer o checklist Q1–Q13.
5. Emitir o veredito consolidado (formato abaixo).
6. Se Q9 falhou, sugerir `/eng-ia-micro-decisoes` antes do commit.

## Formato de saída

```markdown
# Quality Gate — <escopo>

## code-review (4 camadas)
- 🔴 <crítico> ... (arquivo:linha)
- 🟡 <atenção> ...

## code-philosophy (5 Leis)
- Fail Fast: PASS
- Intentional Naming: FAIL — <onde>

## Checklist Sandeco
| # | Critério | Resultado |
|---|---|---|
| Q1 POO | PASS |
| Q4 Token/tamanho | FAIL — services.py com 900 linhas |
| Q10 TDD | FAIL — sem teste para parser de pedido |
| ... | ... |

## Veredito
- PASS / PASS COM RESSALVAS / FAIL
- Bloqueadores: <lista>
- Recomendações: <lista>
```

## Se o que está sendo entregue é uma aplicação web

O checklist Q1–Q13 cobre **código**. Ele não cobre o que só aparece depois que a aplicação está servindo gente. Antes de dar PASS em algo que vai pro navegador, rode as três validações de `16-seguranca/seguranca-app/`:

| Validação | Pergunta | Skill |
|---|---|---|
| **Segredo no front** | abrindo o DevTools do site em produção, dá pra achar chave/token no bundle? | `16-seguranca/segredos-no-front/` |
| **Sessão e cookies** | apagar o cookie derruba a sessão? A API responde sem o header de auth? Trocar o ID mostra dado de outro? | `16-seguranca/sessao-e-cookies/` |
| **Rate limit** | rota de login/cadastro/IA aguenta 200 requisições em 10 segundos sem cobrar você por isso? | `16-seguranca/rate-limit-e-abuso/` |

Não é redundância com o Q12: **Q12 olha o diff, essas três olham o que está no ar.** Um repo com segredo zero no histórico ainda pode publicar a chave no bundle.

## O que NÃO fazer

- Não reescrever a lógica de `code-review`/`code-philosophy` aqui — chamá-las.
- Não bloquear o commit por conta própria; entregar o veredito e deixar a decisão com o humano.
- Não rodar análise de AST nova — se precisar de profundidade, é `code-review` quem faz.
