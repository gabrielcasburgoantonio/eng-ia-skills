---
name: eng-ia-deploy
description: "Fase 5 do metodo Sandeco: colocar a aplicacao no ar com seguranca (Docker -> VPS -> CI/CD). Use quando for fazer deploy, subir app em VPS/nuvem (LightSail, EC2, Cloud Run), montar Dockerfile/docker-compose, configurar GitHub Actions/branch protection, ou blindar segredos e custo. Nao substitui o quality-gate (pre-commit) — vem depois dele, na entrega."
license: MIT
metadata:
  author: toni
  version: 1.0.0
  framework: eng-ia
  role: deploy
  phase: 5-deploy
  source: Curso 'Engenharia de Software com Agentes Inteligentes' (Prof. Sandeco Macedo), aula 14 (extra, convidado Rogerio/UFG)
  compatibility: Claude Code, Codex, Cursor, Gemini CLI e demais agentes compatíveis com Agent Skills.
---

# eng-ia-deploy, colocar no ar sem tomar prejuizo

Ultima fase do pipeline eng-ia. O deploy **profissionaliza** a aplicacao (mais valor percebido = da pra cobrar), mas e onde mora o risco de **fatura surpresa** e **invasao**. Esta skill e um checklist de entrega segura, nao um tutorial de um provedor especifico.

> Regra zero: **segredo nunca no repositorio, repo privado por padrao.** Chave de API em repo publico e drenada por robos em horas. Isso ja e o Q12 do `eng-ia-quality-gate`; aqui vira operacao.

## Quando rodar

- A feature passou no `/eng-ia-quality-gate` e vai pra producao/homologacao.
- Precisa empacotar (Docker), escolher onde hospedar (VPS/nuvem), ou automatizar deploy (CI/CD).

## 1. Empacotar (Docker) — resolve "na minha maquina funciona"

- **Dockerfile** = receita (passo a passo) -> `build` -> **imagem** (foto imutavel, compartilhavel) -> **container** (execucao isolada).
- Use **imagem base da comunidade** (`python:3.13-alpine` etc.) — Alpine e leve. Nao instale do zero.
- **docker-compose** (`docker compose up -d`) sobe varios servicos (app + Redis + banco) de um arquivo so; use `restart: always`, `healthcheck` e **volumes** para persistir dados.

## 2. Onde hospedar (VPS/nuvem)

| | Simplificado | Poderoso |
|---|---|---|
| AWS | **LightSail** — preco **fixo**, "feijao com arroz" | **EC2** — variavel, **risco de fatura 10x** por config errada |
| Perfil | comeco, custo previsivel | autoscaling, redundancia, ajuste fino |

Vale o mesmo para Google (Cloud Run) e VPS fora das big techs. **Regra:** comece no simples e previsivel. "Existem dois tipos: os que ja tomaram prejuizo com AWS e os que vao tomar."

## 3. Blindagem de custo e seguranca (o bolso e a porta)

Checklist obrigatorio antes de deixar no ar:

1. **Custo diario** monitorado nos 3 primeiros dias (Billing/Cost Explorer, granularidade diaria) — pega config errada cedo.
2. **Cartao virtual por servico** (Nubank/Inter) — cancela sem mexer no principal.
3. **Cancelamento completo**: deletar instancia NAO basta — apagar **volume** e **IAM/permissao**, senao continua cobrando.
4. **Portas**: fecha tudo, abre so o necessario. SSH(22) -> mover pra **porta >10000**; banco (5432) -> porta aleatoria. Firewall do provedor antes do Linux.
5. **Cloudflare tunnel** (gratuito): oculta o IP real, resolve IP dinamico do reboot, anti-DDoS. Robo so bate no Cloudflare.
6. **Banco nunca exposto** — banco aberto na internet e invadido em ~1 dia (relatos de resgate em Bitcoin).
7. **App fora da home** do usuario — criar `/data` (com `sudo`), acessivel a todos.

## 4. Git / CI-CD (a parte que mais toca o metodo)

- **Repositorio privado** sempre. **`main` protegida** (so 1-2 pessoas dao merge) + `develop` (homologacao) + branch por feature.
- **Pull Request + revisor** aprova antes do merge; **revert** de 1 clique volta no tempo.
- **Pre-commit hooks** (guardrail determinístico, Q12): `ruff format`, ordena/remove imports, **detecta chave de API e bloqueia o commit**, docstring, `mypy`. O LLM ignora prompt; nao ignora hook.
- **Deploy automatico (GitHub Actions)** — estagios de evolucao, do simples ao blindado:
  1. `git pull` manual no servidor
  2. Action conecta no servidor -> puxa codigo -> sobe
  3. Producao **sem repo no servidor** (SCP/rsync) — mais seguro

## 5. Diretiva no CLAUDE.md do projeto

Ao chegar na fase de deploy, registre as regras como diretiva (nao como permissao solta):

```markdown
## Deploy (fase 5 / eng-ia-deploy)
- Repo privado; segredo so em .env local + secret manager, nunca commitado.
- main protegida; merge so via PR com revisor.
- Empacotar com Docker; subir via docker-compose (restart: always, healthcheck).
- VPS: portas minimas, SSH >10000, banco nunca exposto, Cloudflare tunnel.
- Monitorar custo diario na 1a semana.
```

## Como combinar

- `eng-ia` — esta e a **fase 5** do indice.
- `eng-ia-quality-gate` — o **Q12** (pre-commit/secret-scan) e barrado ANTES; aqui ele vira operacao de entrega.
- `eng-ia-bootstrap` — pode ja ter oferecido `.pre-commit-config.yaml`, `Dockerfile` e `docker-compose.yml` minimos no comeco do projeto.
- `eng-ia-agent-harness` — DevContainer (aula 13) e primo do Docker: isolamento; o mesmo raciocinio de "gaiola descartavel".

## Origem pedagogica

Aula 14 do curso Engenharia de Software com Agentes Inteligentes (extra, convidado Rogerio Silva/UFG; Sandeco de ferias). Abre o "mes de VPS" pos-livro (AWS/Rogerio, Google Cloud Run/Daniel, VPS independente/Marcos Devolder). Nao e conteudo do livro — e o degrau operacional que coloca o software gerado pelo metodo no ar. Aplicacao-demo: o RAG dos documentos do Edson (Streamlit + Redis via Docker).
