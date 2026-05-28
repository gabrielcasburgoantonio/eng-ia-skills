---
name: eng-ia-micro-decisoes
description: >
  Registra micro-decisões — a metodologia nova do Prof. Sandeco para dar memória institucional a projetos
  assistidos por IA. Cada entrada tem quatro linhas (~30 tokens): qual foi o problema, qual foi o atrito,
  quem resolveu, quem aprovou. Use quando o usuário digitar "/eng-ia-micro-decisoes", "registra essa decisão",
  "anota esse atrito", ou sempre que houver discordância/concordância relevante entre humano e IA, ou uma
  escolha de arquitetura que o futuro-você (ou a IA, que não lembra do passado) vai precisar reconstituir.
  Entrega: append em `.eng-ia/micro-decisoes.md`.
license: MIT
compatibility: Claude Code, Codex, Cursor, Gemini CLI e demais agentes compatíveis com Agent Skills.
metadata:
  author: toni
  version: "1.0.0"
  framework: eng-ia
  role: memory-ledger
  source: "Curso 'Engenharia de Software com Agentes Inteligentes' (Prof. Sandeco Macedo), aula 08 (micro-decisões)"
---

# eng-ia-micro-decisoes, memória institucional do projeto

> "A IA não lembra das coisas que aconteceram antes. Esse atrito que a gente teve, concordância e discordância, tem que ser registrado. Tudo em micro-decisões. São coisas em quatro linhas pequenininhas. Consome 30 tokens, no máximo." — Sandeco, aula 08

O LLM começa cada sessão do zero. As decisões que vocês tomaram juntos — por que escolheram X em vez de Y, onde a IA errou e você corrigiu — evaporam. Esta skill captura isso em entradas mínimas, baratas de escrever e de ler.

## O formato (rígido — quatro campos)

Cada micro-decisão tem exatamente:

1. **Problema** — qual era a questão em jogo.
2. **Atrito** — onde houve discordância ou tensão (humano x IA, ou trade-off).
3. **Quem resolveu** — quem propôs a saída.
4. **Quem aprovou** — quem bateu o martelo.

Mantenha curto. Se uma entrada passa de ~5 linhas, ela virou outra coisa (vai pra spec, não pro ledger).

## Onde grava

Append em `.eng-ia/micro-decisoes.md` (crie via `eng-ia-bootstrap` se não existir). Nunca reescreve entradas antigas — só adiciona.

### Cabeçalho (criar uma vez, se o arquivo for novo)

```markdown
# Micro-decisões

Registro mínimo de atritos e acordos humano-IA. Quatro linhas por entrada, ~30 tokens.
Metodologia: Sandeco, "Engenharia de Software com Agentes Inteligentes", aula 08.
```

### Modelo de entrada (append)

```markdown
## MD-NNN — <título curto> — <AAAA-MM-DD>
- **Problema:** ...
- **Atrito:** ...
- **Resolveu:** ...
- **Aprovou:** ...
```

`NNN` é sequencial (MD-001, MD-002...). Olhe o último ID no arquivo antes de incrementar.

## Procedimento

1. Ler `.eng-ia/micro-decisoes.md`. Se ausente, criar com o cabeçalho.
2. Descobrir o próximo ID sequencial.
3. Preencher os quatro campos a partir do que aconteceu na conversa. Se algum campo for ambíguo, **perguntar** — não inventar quem aprovou.
4. Append da entrada. Confirmar com uma linha ("Registrado MD-007").

## Quando registrar (gatilhos)

- A IA propôs algo e o humano discordou e impôs outra direção.
- Escolha de arquitetura/biblioteca/padrão com trade-off real.
- Mudança de rumo no meio de uma feature.
- Bug que revelou uma premissa errada.

## Quando NÃO registrar

- Decisão trivial sem atrito ("renomear variável").
- Conteúdo que pertence à spec (requisito, design) — vai pra `specs/`, não pro ledger.
- Detalhe que não muda nenhuma decisão futura.
