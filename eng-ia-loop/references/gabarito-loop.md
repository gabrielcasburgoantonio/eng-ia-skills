# Gabarito — documento `<nome>-loop.md`

> Estrutura derivada da skill `sandeco-loop` (`sandeco/prompts` @ `3bf5a6a`, Prof. Sandeco Macedo), reescrita para o hub. O padrão de origem é a Loop Library (gatilho + meta + verificação + parada).

Um arquivo só. Toda seção é obrigatória **exceto Entradas** (e Sub-loops, que só existe se houver aninhamento). Seção que não se aplica: escreva "não se aplica" e diga por quê.

## Entrevista antes do gabarito (uma pergunta por vez)

1. **Meta** — qual o estado final concreto? *(Entradas: o loop precisa pedir algo a cada execução — alvo, tema, nº de voltas? Se não, a seção some.)*
2. **Verificável?** — número/teste/comando, ou depende de juízo?
3. **Check** — que comando ou aferição prova a volta? Prefira determinístico (teste, lint, build, contagem, diff).
4. **Gatilho** — manual, agendado ou por evento?
5. **Paradas** — além de sucesso: sem-progresso, bloqueado, esgotado.
6. **Skills chamadas** — quais skills nomeadas a volta usa? Há sub-loop?
7. **Memória** — que arquivo guarda progresso, decisões e o que já foi tentado?
8. **Guardrails** — teto de turnos/custo; onde exige aprovação humana (destrutivo, produção, financeiro, externo)?

## Esqueleto

```markdown
---
nome: <nome-do-loop>
categoria: <Avaliação | Cobertura | Refatoração | Multi-agente | ...>
gatilho: <manual | agendado | evento>
base-teorica: <fontes que sustentam o desenho>
---

# <Nome legível>

## Descrição
<1-2 frases>

## Use quando
<situação concreta>

## Entradas            ← OPCIONAL: só se o loop pede valores ao rodar
1. <entrada>: <o que é>. Sem resposta → padrão sensato, e AVISE na conversa que usou o padrão.

## Meta
<estado final>. Verificável? <sim: número/comando | não: juízo, endurecido abaixo>.

## Verificação (o check que manda)
A cada volta, RODE o check e COLE a saída na conversa — é a evidência que o avaliador do /goal lê.
- Check: `<comando>`  OU  juiz endurecido (rubrica congelada, gerador ≠ juiz, sem histórico compartilhado).
- Pronto = <condição lida dessa saída>.

## Passos da volta
0. (só com Entradas, 1ª volta) pergunte e TRAVE as entradas para a execução inteira.
1. Fotografe o estado atual (linha de base).
2. Escolha o alvo de maior impacto (pior primeiro).
3. Faça UMA mudança, via <skills nomeadas> ou disparando um sub-loop declarado.
4. Rode o check e cole a saída.
5. Mantenha só se nada regrediu; senão reverta.
6. Registre em <arquivo de estado>.

## Estados de parada
- sucesso: <condição do check>
- sem-progresso: 2 voltas sem ganho medível
- bloqueado: <quando chamar humano>
- esgotado: <N turnos / R$ X>
Erro ou teto estourado NUNCA é sucesso.

## Guardrails
- Teto: <N turnos / R$ X>  (com sub-loop, o teto conta pai × filho)
- Aprovação humana antes de: <irreversível / produção / financeiro / externo>

## Memória / estado
<arquivo no disco>

## Sub-loops            ← OPCIONAL
- `<sub>-loop.md` (mesma pasta) — disparado no passo <N>; roda até a parada dele. Proibido ciclo.

## Por que funciona
<qual modo de falha cada decisão evita>

## Como acionar
- Cabe no contexto → `/goal Use o loop <nome>. Continue até <estado medível> comprovado por <check no transcript>. Restrições: <...>. Pare e me chame se <bloqueio>, ou após <N> turnos.`
- Estoura o contexto → esqueleto Ralph (laço de shell que relê este documento + estado no disco a cada volta; sandbox + teto de custo).

## Métrica de saúde
custo por mudança aceita = tokens (ou R$) ÷ mudanças que sobreviveram ao check
```

## Comando `/loop-<nome>` (só se pedido)

Arquivo `loop-<nome>.md` em `~/.claude/commands/` (loop global) ou `./.claude/commands/` (loop local):

```markdown
---
description: Roda o loop <nome> (<caminho do loop>) e entrega <o quê>
argument-hint: "[entradas inline; vazio = o loop pergunta]"
---

Execute o loop em `<caminho absoluto do <nome>-loop.md>`. Leia o documento primeiro e siga os passos da volta à risca.
<resumo: entradas a travar, check a colar no transcript a cada volta, condição de parada. Erro ou teto estourado nunca é sucesso.>

Entradas inline: $ARGUMENTS
```

Funciona sozinho (`/loop-<nome>`) e embrulhado (`/goal /loop-<nome>`).

## Exemplo curto

`cobertura-auth-loop.md` — entrada: diretório alvo (`test/auth`); meta: 100% de cobertura nele; check: `npm test -- --coverage` sai 0 e mostra 100%; volta: ataca o arquivo mais descoberto, escreve 1 teste, prova vermelho→verde, roda a suíte; paradas: 100% / 2 voltas sem ganho / 30 turnos; guardrail: não tocar em CI sem aprovação.
