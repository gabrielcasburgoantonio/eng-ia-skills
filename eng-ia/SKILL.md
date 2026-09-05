---
name: eng-ia
description: 'Skill-indice do metodo Sandeco (Engenharia de Software com Agentes). Roteia qual skill chamar em cada fase: Agent Harness, spec SDD, teste TDD, codigo e quality gate. Nao gera artefato. Acione com: /eng-ia, metodo Sandeco, engenharia de IA, agent harness, qual skill usar.'
license: MIT
metadata:
  author: toni
  version: 1.0.0
  framework: eng-ia
  role: index-router
  source: Curso 'Engenharia de Software com Agentes Inteligentes' (Prof. Sandeco Macedo), aulas 01-14 + livro homônimo (final, 165p)
  compatibility: Claude Code, Codex, Cursor, Gemini CLI e demais agentes compatíveis com Agent Skills.
---

# eng-ia, o método de Engenharia de Software com Agentes Inteligentes

Esta skill é o **mapa** do método do Prof. Sandeco. Ela não produz artefato: ela decide em que fase você está e qual skill chamar. Pense nela como o orquestrador mental do pipeline.

## A tese (não esquecer)

> **IA + processo estruturado = software sustentável.**

O oposto é o *vibe coding*: pedir código solto ao LLM, aceitar o que vier, e acumular dívida até o projeto travar. O método Sandeco troca improviso por processo. A IA continua escrevendo o código — mas dentro de trilhos que um humano definiu.

Princípios que atravessam todas as fases:

- **POO obrigatória.** Modelar em classes com responsabilidade única antes de gerar código.
- **Alta coesão, baixo acoplamento.** Cada arquivo faz uma coisa. Arquivos pequenos = menos tokens por leitura = LLM menor (e mais barato) consegue atuar. **Token é custo.** O princípio vale TAMBÉM para a spec: quebrar em múltiplos documentos coesos (PRD, arquitetura, API, rules, tasks, testes) em vez de um documento-monstro.
- **Spec antes de código.** Ambiguidade vira bug. Resolva no texto, não no runtime. A spec deixa de ser documentação e vira o centro do projeto — código é derivação dela.
- **Harness antes de execucao.** Spec sozinha e prompt; modelo sozinho e forca bruta. Agent Harness e o conjunto de mecanismos que toca o modelo/agente e guia, verifica, limita, conecta ou recupera a execucao.
- **Teste antes do código (TDD).** Spec → Test → Code → Refactor (Red/Green/Refactor, XP/Kent Beck). A IA não tem a preguiça humana que matou o TDD nos anos 2010 — ela escreve o teste primeiro sem reclamar. Manutenção corretiva cai drasticamente.
- **Padrões de projeto são vocabulário.** Você não implementa o pattern à mão; você o *nomeia* para a IA implementar certo.
- **Skill ensina, hook vigia.** A skill ensina o agente a fazer algo novo; o hook é determinístico e intercepta o que o agente faz. O LLM pode ignorar um prompt; não pode ignorar um hook.
- **Registrar o atrito.** Toda discordância/concordância humano-IA vira micro-decisão. A IA não lembra do passado; a memória institucional é escrita.
- **Specs valem além de software.** O método se aplica a qualquer artefato gerado por IA: pesquisa científica, roteiro de vídeo, acervo técnico, etc.

## O pipeline e qual skill chamar em cada fase

| Fase | O que fazer | Skill a chamar |
|---|---|---|
| -1. Desenho do harness | Auditar se há contexto, memória, loop, ferramentas, verificação, guardrails, fallback e observabilidade suficientes | **`eng-ia-agent-harness`** |
| 0. Bootstrap do projeto | Montar a estrutura concreta do Agent Harness no projeto (CLAUDE.md, specs/, tests/, regras globais, hooks opcionais) | **`eng-ia-bootstrap`** |
| 0.5 Ideia -> brief | Ideia ainda bruta: clarear problema, valor, público, premissas perigosas antes de especificar | **Ideation Team do Reversa** (`/reversa-new`) — mais enxuto que o brainstorm do BMAD |
| 1. Requisitos -> Spec | Decompor em componentes e escrever specs SDD com score (múltiplos docs: PRD, arquitetura, API, rules) | **`reversa-spec-sdd`** (ou `sdd-spec`) — ou **`eng-ia-frameworks`** para escolher/operar BMAD/SpecKit/Reversa. Delta pequeno numa base já entendida: **`reversa-code-express`** |
| 2. Quebra em tarefas | Tarefas atômicas T001/T002 com dependências e paralelismo | **`reversa-to-do`** |
| 3a. Teste primeiro (TDD) | Escrever o teste a partir da spec antes do código. Red/Green/Refactor. Pirâmide: unitário → integração → contrato → end-to-end → regressão | (regra global no CLAUDE.md via `eng-ia-bootstrap`) |
| 3b. Modelagem + código | Aplicar as 5 Leis da Defesa Elegante ao gerar/revisar lógica | **`code-philosophy`** |
| 4. Quality gate | Revisão antes de subir: corretude/segurança/perf/estilo + critérios Sandeco + verificação de TDD | **`eng-ia-quality-gate`** (que chama `code-review` + `code-philosophy`) |
| 5. Deploy | Colocar no ar (Docker → VPS → CI/CD), com segredos e branch/PR protegidos | **`eng-ia-deploy`** (aula 14) |
| Transversal | Automatizar tarefa iterativa (meta + verificação + fuga) em loop seguro | **`eng-ia-loop`** |
| Transversal | Registrar atritos e acordos humano-IA ao longo de tudo | **`eng-ia-micro-decisoes`** |

## Como usar na prática

1. **Projeto novo ou agente novo?** Se a arquitetura do agente ainda está nebulosa, comece por `/eng-ia-agent-harness`. Se a decisão já está clara, rode `/eng-ia-bootstrap` para criar a estrutura no projeto.
2. **Já tem harness, vai começar uma feature?** `/reversa-spec-sdd` para a spec, depois `/reversa-to-do` para as tarefas.
3. **Hora de codificar?** **Escreva o teste antes** (Red/Green/Refactor). A IA gera o teste a partir da spec, depois o código que passa nele. Mantenha as 5 Leis (`code-philosophy`) à mão. Cross-referencie: ID da spec no comentário do código, caminho do arquivo na spec.
4. **Antes de commitar?** `/eng-ia-quality-gate`.
5. **Discordou da IA, ou tomou uma decisão de arquitetura no meio do caminho?** Registre com `/eng-ia-micro-decisoes` — quatro linhas, ~30 tokens.

## O que esta skill NÃO faz

- Não gera specs (isso é `reversa-spec-sdd`).
- Não gera lista de tarefas (isso é `reversa-to-do`).
- Não roda análise de AST nem revisão de código (isso é `code-review`).

Ela só te diz onde você está no método e para onde ir.

## Referências de origem

Método extraído das 14 videoaulas + livro final do Prof. Sandeco Macedo. As novidades do curso (micro-decisões como metodologia, distinção skill-vs-hook, token-como-custo aplicado a coesão) estão concentradas na **aula 08** e foram a base das skills `eng-ia-micro-decisoes`, `eng-ia-bootstrap` e `eng-ia-quality-gate`. A **aula 09** aprofunda SDD (múltiplos documentos coesos, não-objetivos no PRD, specs para qualquer artefato) e introduz TDD com a pirâmide de 5 camadas (unitário, integração, contrato, end-to-end, regressão). A **aula 10** define Agent Harness com rigor (`eng-ia-agent-harness`). A **aula 11** traz os frameworks BMAD/SpecKit/Reversa (`eng-ia-frameworks`). A **aula 12** traz a engenharia de looping (`eng-ia-loop`). As **aulas 13-14** fecham o livro com DevContainer + YOLO (execução autônoma segura) e deploy em VPS (Docker/Git/CI-CD).

