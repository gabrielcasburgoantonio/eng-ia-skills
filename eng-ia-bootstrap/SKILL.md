---
name: eng-ia-bootstrap
description: >
  Monta o Agent Harness de um projeto novo (ou retrofita um existente) segundo o método Sandeco:
  cria/atualiza o CLAUDE.md com as regras globais de engenharia (POO obrigatória, alta coesão/baixo acoplamento,
  token=custo, spec antes de código, git obrigatório), cria a estrutura specs/ e .eng-ia/, e aponta qual skill
  usar em cada fase. Use quando o usuário digitar "/eng-ia-bootstrap", "configura o harness desse projeto",
  "prepara esse projeto pra trabalhar com agente", ou ao iniciar qualquer projeto novo que vá usar o método eng-ia.
  Entrega: CLAUDE.md, specs/.gitkeep, .eng-ia/micro-decisoes.md e um resumo dos próximos passos.
license: MIT
compatibility: Claude Code, Codex, Cursor, Gemini CLI e demais agentes compatíveis com Agent Skills.
metadata:
  author: toni
  version: "1.0.0"
  framework: eng-ia
  role: setup
  phase: "0-bootstrap"
  source: "Curso 'Engenharia de Software com Agentes Inteligentes' (Prof. Sandeco Macedo), aula 08 (Agent Harness)"
---

# eng-ia-bootstrap, montar o Agent Harness

Um **Agent Harness** é o conjunto de configs + skills + regras + hooks que transforma um agente genérico num agente especializado para o seu projeto. Esta skill monta esse arnês.

## Quando rodar

- Projeto novo que vai usar o método eng-ia.
- Projeto existente sem `CLAUDE.md` (ou com um fraco) que você quer colocar nos trilhos.

## O que ela cria

```
<projeto>/
├── CLAUDE.md                 regras do projeto + ponteiros para as skills do método
├── specs/                    specs SDD por feature (geradas depois por reversa-spec-sdd)
│   └── .gitkeep
├── tests/                    espelha src/ — testes vêm antes do código (TDD)
│   └── .gitkeep
└── .eng-ia/
    └── micro-decisoes.md     ledger de atritos/acordos humano-IA (gerido por eng-ia-micro-decisoes)
```

## Procedimento

### 1. Detectar contexto

- Existe `CLAUDE.md`? Se sim, **não sobrescreva** — proponha um patch que injeta a seção "Regras de engenharia (método eng-ia)" preservando o resto.
- Já existe `.git`? Se não, avise que git é obrigatório no método (não inicialize sem confirmar com o usuário).
- Linguagem/stack do projeto (olhe arquivos de manifesto: package.json, pyproject.toml, etc.) para adaptar os exemplos.

### 2. Escrever/atualizar o CLAUDE.md

Injete esta seção (adapte ao stack detectado, não cole cru):

```markdown
## Regras de engenharia (método eng-ia / Sandeco)

- **POO obrigatória.** Modele em classes de responsabilidade única antes de gerar código.
- **Alta coesão, baixo acoplamento.** Um arquivo = uma responsabilidade. Arquivo pequeno
  = menos tokens por leitura = LLM menor e mais barato consegue atuar. Token é custo.
- **Spec antes de código.** Toda feature começa por `specs/<feature>/` (use /reversa-spec-sdd).
  Ambiguidade se resolve no texto, não em runtime. A spec pode ser múltiplos documentos
  coesos (PRD, arquitetura, API, rules, tasks) em vez de um doc-monstro.
- **TDD obrigatório.** Spec → Test → Code → Refactor. A IA gera o teste primeiro a partir
  da spec (Red), depois o código que passa nele (Green), depois melhora (Refactor).
  Testes vivem em `tests/` espelhando `src/`. Pirâmide: unitário → integração → contrato
  → end-to-end → regressão (bug encontrado vira teste para nunca mais voltar).
- **Cross-reference.** ID da spec no comentário do código; caminho do arquivo na spec.
- **Padrões de projeto = vocabulário.** Nomeie o pattern para a IA implementar certo;
  não peça implementação artesanal.
- **Git obrigatório.** Commits pequenos e descritivos. Nada sobe sem passar pelo
  /eng-ia-quality-gate.
- **Registre o atrito.** Discordâncias e decisões de arquitetura viram micro-decisões
  em `.eng-ia/micro-decisoes.md` (use /eng-ia-micro-decisoes).

### Qual skill em cada fase
| Fase | Skill / regra |
|---|---|
| Spec | /reversa-spec-sdd |
| Tarefas | /reversa-to-do |
| Teste primeiro | TDD obrigatório (regra acima) — gerar teste a partir da spec antes do código |
| Código (5 Leis) | code-philosophy |
| Quality gate | /eng-ia-quality-gate |
| Memória | /eng-ia-micro-decisoes |
```

### 3. Criar a estrutura

- `specs/.gitkeep` (vazio).
- `tests/.gitkeep` (vazio). A estrutura deve espelhar `src/` quando ela existir.
- `.eng-ia/micro-decisoes.md` com o cabeçalho do ledger (ver skill `eng-ia-micro-decisoes` para o formato exato).

### 4. Hooks (opcional, perguntar antes)

Hooks são determinísticos: o LLM pode ignorar um prompt, não pode ignorar um hook. Ofereça (sem instalar sem consentimento) ganchos úteis:

- **PreToolUse / Write**: bloquear escrita de arquivo de código sem spec correspondente em `specs/`.
- **PostToolUse / Bash(git commit)**: lembrar de registrar micro-decisão se houve discordância na sessão.

Se o usuário aceitar, instale em `.claude/settings.json` do projeto (não no global) e descreva o que cada hook faz.

### 5. Fechar com os próximos passos

Imprima um resumo: o que foi criado, e a sequência recomendada — `/reversa-spec-sdd` para a primeira feature, depois `/reversa-to-do`.

## O que NÃO fazer

- Não sobrescrever CLAUDE.md existente sem mostrar o diff.
- Não inicializar git, instalar hooks ou criar arquivos fora do projeto sem confirmar.
- Não gerar specs aqui — isso é fase 1 (`reversa-spec-sdd`).
