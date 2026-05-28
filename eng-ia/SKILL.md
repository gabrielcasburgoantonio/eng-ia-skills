---
name: eng-ia
description: >
  Skill-índice do método de Engenharia de Software com Agentes Inteligentes do Prof. Sandeco Macedo.
  Codifica o pipeline completo: requisitos -> spec (SDD) -> modelagem POO -> código -> quality gate -> micro-decisões,
  com a tese central "IA + processo estruturado = software sustentável" (anti vibe-coding).
  Use quando o usuário digitar "/eng-ia", "método Sandeco", "engenharia de IA", "como estruturar esse projeto com agente",
  ou quando precisar decidir QUAL skill chamar em cada fase de um projeto assistido por IA.
  Não gera artefato próprio: roteia para reversa-spec-sdd, reversa-to-do, code-philosophy, code-review,
  eng-ia-bootstrap, eng-ia-micro-decisoes e eng-ia-quality-gate.
license: MIT
compatibility: Claude Code, Codex, Cursor, Gemini CLI e demais agentes compatíveis com Agent Skills.
metadata:
  author: toni
  version: "1.0.0"
  framework: eng-ia
  role: index-router
  source: "Curso 'Engenharia de Software com Agentes Inteligentes' (Prof. Sandeco Macedo), aulas 01-08 + livro homônimo"
---

# eng-ia, o método de Engenharia de Software com Agentes Inteligentes

Esta skill é o **mapa** do método do Prof. Sandeco. Ela não produz artefato: ela decide em que fase você está e qual skill chamar. Pense nela como o orquestrador mental do pipeline.

## A tese (não esquecer)

> **IA + processo estruturado = software sustentável.**

O oposto é o *vibe coding*: pedir código solto ao LLM, aceitar o que vier, e acumular dívida até o projeto travar. O método Sandeco troca improviso por processo. A IA continua escrevendo o código — mas dentro de trilhos que um humano definiu.

Princípios que atravessam todas as fases:

- **POO obrigatória.** Modelar em classes com responsabilidade única antes de gerar código.
- **Alta coesão, baixo acoplamento.** Cada arquivo faz uma coisa. Arquivos pequenos = menos tokens por leitura = LLM menor (e mais barato) consegue atuar. **Token é custo.**
- **Spec antes de código.** Ambiguidade vira bug. Resolva no texto, não no runtime.
- **Padrões de projeto são vocabulário.** Você não implementa o pattern à mão; você o *nomeia* para a IA implementar certo.
- **Skill ensina, hook vigia.** A skill ensina o agente a fazer algo novo; o hook é determinístico e intercepta o que o agente faz. O LLM pode ignorar um prompt; não pode ignorar um hook.
- **Registrar o atrito.** Toda discordância/concordância humano-IA vira micro-decisão. A IA não lembra do passado; a memória institucional é escrita.

## O pipeline e qual skill chamar em cada fase

| Fase | O que fazer | Skill a chamar |
|---|---|---|
| 0. Bootstrap do projeto | Montar o Agent Harness (CLAUDE.md, specs/, regras globais, hooks opcionais) | **`eng-ia-bootstrap`** |
| 1. Requisitos -> Spec | Decompor em componentes e escrever specs SDD com score | **`reversa-spec-sdd`** (ou `sdd-spec`) |
| 2. Quebra em tarefas | Tarefas atômicas T001/T002 com dependências e paralelismo | **`reversa-to-do`** |
| 3. Modelagem + código | Aplicar as 5 Leis da Defesa Elegante ao gerar/revisar lógica | **`code-philosophy`** |
| 4. Quality gate | Revisão antes de subir: corretude/segurança/perf/estilo + critérios Sandeco | **`eng-ia-quality-gate`** (que chama `code-review` + `code-philosophy`) |
| Transversal | Registrar atritos e acordos humano-IA ao longo de tudo | **`eng-ia-micro-decisoes`** |

## Como usar na prática

1. **Projeto novo?** Comece por `/eng-ia-bootstrap`. Ele cria o harness e aponta para as próximas fases.
2. **Já tem harness, vai começar uma feature?** `/reversa-spec-sdd` para a spec, depois `/reversa-to-do` para as tarefas.
3. **Escrevendo código?** Mantenha as 5 Leis (`code-philosophy`) à mão. Cross-referencie: ID da spec no comentário do código, caminho do arquivo na spec.
4. **Antes de commitar?** `/eng-ia-quality-gate`.
5. **Discordou da IA, ou tomou uma decisão de arquitetura no meio do caminho?** Registre com `/eng-ia-micro-decisoes` — quatro linhas, ~30 tokens.

## O que esta skill NÃO faz

- Não gera specs (isso é `reversa-spec-sdd`).
- Não gera lista de tarefas (isso é `reversa-to-do`).
- Não roda análise de AST nem revisão de código (isso é `code-review`).

Ela só te diz onde você está no método e para onde ir.

## Referências de origem

Método extraído das 8 videoaulas + livro do Prof. Sandeco Macedo. As novidades do curso (micro-decisões como metodologia, distinção skill-vs-hook, token-como-custo aplicado a coesão) estão concentradas na **aula 08** e foram a base das skills `eng-ia-micro-decisoes`, `eng-ia-bootstrap` e `eng-ia-quality-gate`.
