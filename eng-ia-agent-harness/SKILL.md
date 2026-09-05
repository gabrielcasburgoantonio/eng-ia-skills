---
name: eng-ia-agent-harness
description: "Projeta e audita Agent Harness para projetos, agentes e fluxos com LLM. Use quando o usuario pedir para montar um agente, definir o harness, avaliar se uma config/skill/hook/RAG/memoria/loop e harness, escolher guardrails, preparar um projeto antes da spec/codigo, ou criar a fase zero/menos-um de um workflow com IA. Especialmente util para evitar achar que spec, prompt ou configuracao solta bastam."
---

# eng-ia-agent-harness

Use esta skill para transformar uma ideia com LLM em um **Agent Harness** operacional: o conjunto de mecanismos que toca o modelo/agente e ajuda, guia, controla, verifica ou facilita a execucao da tarefa.

A tese da aula 10 e direta: **spec sozinha e prompt; modelo sozinho e forca bruta; harness e o que liga a tarefa ao modelo com confianca**.

## Definicao curta

Um mecanismo e Agent Harness quando passa pelos dois testes:

1. **Toca o agente/modelo?** Entra no input, output, contexto, chamada de ferramenta, loop, memoria, runtime, verificador, fallback ou decisao de execucao do agente?
2. **Ajuda a acao?** Guia, controla, restringe, conecta, verifica, reduz custo, recupera erro, fornece contexto ou aumenta confiabilidade?

Se as duas respostas forem sim, trate como harness. Se nao toca LLM/agente, pode ser harness de outro sistema, mas nao Agent Harness.

## Distincoes

- **Guardrail** e subconjunto de harness: limita, restringe ou bloqueia.
- **Harness** e maior: alem de limitar, tambem guia, conecta, executa, verifica, recupera e melhora a acao.
- **Prompt comum do usuario** e input, nao harness.
- **Prompt estruturado com tecnica, system prompt, regras ou guardrails** pode ser harness, porque toca e facilita o modelo.
- **Configurar um harness pronto** nao torna alguem engenheiro de harness. Engenharia aparece quando ha problema identificado, viabilidade pensada e mecanismo construido ou composto para resolver.
- **Spec nao substitui harness.** A spec e a partitura/carga; o harness e a estrutura que permite ao agente executar com confianca.

## Workflow

### 1. Delimitar a tarefa

Escreva em 3 linhas:

```markdown
Objetivo final: ...
Agente/modelo envolvido: ...
Risco principal se executar sem harness: ...
```

Se o objetivo ainda estiver vago, chame `reversa-spec-sdd` ou `prd-manager` antes de prosseguir. Esta skill nao substitui PRD/spec; ela desenha a camada que faz a spec virar execucao confiavel.

### 2. Inventariar mecanismos existentes

Liste o que ja existe ao redor do agente:

| Camada | Pergunta | Exemplos |
|---|---|---|
| Contexto | Como o agente recebe informacao relevante? | specs, RAG, arquivos lidos sob demanda, resumo de contexto |
| Memoria | O que persiste entre sessoes? | ADRs, micro-decisoes, decision log, cache |
| Loop | Como o agente itera ate concluir? | plan-execute-verify, SanderClaw-like loop, retry |
| Ferramentas | Que acoes externas o agente pode chamar? | shell, browser, API, MCP, scripts |
| Verificacao | Como a saida e conferida? | testes, scorer, lint, diff, avaliador, review |
| Guardrails | O que limita dano/custo/desvio? | sandbox, allowlist, budget, schema, hook pre/post |
| Fallback | O que acontece se falhar? | retry, troca de modelo, reduzir escopo, pedir humano |
| Observabilidade | Como saber o que aconteceu? | logs, traces, custo, tempo, artefatos |

Marque cada item como `existe`, `fraco`, `ausente`.

### 3. Classificar o que e harness

Para cada mecanismo candidato, aplique:

```markdown
Mecanismo: ...
Toca o agente/modelo? sim/nao
Ajuda/controla/guia/verifica? sim/nao
Classificacao: Agent Harness / harness de sistema / ferramenta / input comum / fora do escopo
Por que importa: ...
```

Evite briga terminologica inutil: se a classificacao nao muda a decisao de projeto, registre a nuance e siga.

### 4. Desenhar o harness minimo

Monte apenas o necessario para a tarefa atual. Um harness minimo costuma ter:

1. **Spec ou objetivo verificavel**: o que precisa acontecer.
2. **Contexto carregavel sob demanda**: onde buscar informacao sem estourar tokens.
3. **Loop de execucao**: planejar, agir, verificar, ajustar.
4. **Ferramentas autorizadas**: o que o agente pode usar.
5. **Verificacao objetiva**: teste, checklist, scorer ou comparacao com criterio.
6. **Guardrails proporcionais**: custo, permissao, seguranca, schema, escopo.
7. **Memoria curta de decisoes**: micro-decisoes/ADRs quando houver atrito.

Se o projeto for de software serio, conecte com `eng-ia-bootstrap`: `CLAUDE.md`, `specs/`, `tests/`, `.eng-ia/micro-decisoes.md`.

### 5. Emitir o parecer

Use este formato:

```markdown
# Agent Harness — <projeto/fluxo>

## Veredito
- Status: suficiente / parcial / insuficiente
- Maior risco: ...
- Proxima acao: ...

## Mecanismos
| Camada | Estado | Decisao |
|---|---|---|
| Contexto | existe/fraco/ausente | ... |
| Memoria | existe/fraco/ausente | ... |
| Loop | existe/fraco/ausente | ... |
| Ferramentas | existe/fraco/ausente | ... |
| Verificacao | existe/fraco/ausente | ... |
| Guardrails | existe/fraco/ausente | ... |
| Fallback | existe/fraco/ausente | ... |
| Observabilidade | existe/fraco/ausente | ... |

## Harness minimo proposto
1. ...
2. ...
3. ...

## O que nao fazer agora
- ...
```

## Como combinar com outras skills

- Use `eng-ia` para decidir a fase do metodo Sandeco.
- Use `eng-ia-bootstrap` quando o parecer indicar que falta estrutura basica no projeto.
- Use `reversa-spec-sdd` quando faltar spec testavel.
- Use `cinturao-regressao` quando o harness precisar prender bug recorrente em teste permanente.
- Use `eng-ia-quality-gate` para verificar antes de commit/entrega.
- Use `eng-ia-micro-decisoes` quando houver decisao sobre trade-off de harness.
- Use `eng-ia-frameworks` quando o harness for operado por um framework SDD (BMAD/SpecKit/Reversa).
- Use `eng-ia-loop` quando a camada de loop/verificacao precisar ser projetada em detalhe.

**Reversa como harness de referencia (estude o padrao):** o framework Reversa e um exemplo vivo e completo das camadas acima — `.reversa/state.json` (memoria + checkpoint entre sessoes), selos 🟢 CONFIRMADO / 🟡 INFERIDO / 🔴 LACUNA (verificacao + confianca), `regression-watch.md` (fallback contra regressao semantica), diretiva "nunca apague/modifique o legado" (guardrail por diretiva, nao por permissao), pausas proativas antes de agente pesado (observabilidade de contexto). Quando montar um harness, vale copiar esses mecanismos.

## Execucao autonoma segura (sandbox concreto — aula 13)

O guardrail de sandbox/limite de pasta se materializa em **DevContainer + YOLO mode**:

- **DevContainer** (extensao Dev Containers no VS Code + Docker) isola o agente numa "gaiola" descartavel. Rode o agente dentro dele.
- **YOLO** (`--dangerously-skip-permissions`) so **dentro de container** — recomendacao oficial da Anthropic. Em terminal comum, o agente pode apagar/estragar arquivos.
- **Diretiva != permissao**: para restringir escopo, use diretiva no CLAUDE.md ("nunca altere a pasta X"; "nunca peca chave de API"), como faz o Reversa com o legado.
- **Contexto**: o "1M de tokens" e marketing; na pratica ~250k degrada. Use `/clear`, `/compact`, memoria (ecoa o loop de Ralph).

## Origem pedagogica

Destilado das aulas 10 e 13 do curso Engenharia de Software com Agentes Inteligentes, Prof. Sandeco Macedo. Os frameworks BMAD/SpecKit/Reversa (aula 11) tem skill propria: `eng-ia-frameworks`.
