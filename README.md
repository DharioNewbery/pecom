# PECOM

Repositório de coordenação do **Sistema de Gestão de Turmas e Tarefas**. Não contém código de aplicação. reúne contrato de API, decisões de arquitetura e orquestração do ambiente local.

## Repositórios do projeto

| Repositório | Descrição |
|---|---|
| [`pecom-api`](../pecom-api/) | Backend em Go/Gin |
| [`pecom-web`](../pecom-web/) | Frontend em Next.js |
| `pecom` (este repo) | Contrato de API, ADRs, orquestração local, documentação geral |

## O que vive aqui

```
pecom/
├── README.md              # visão geral e roadmap (este arquivo)
├── CHANGELOG.md           # mudanças no nível de coordenação (Keep a Changelog)
├── openapi.yaml           # contrato único da API — fonte de verdade  ← ainda a ser criado
├── docs/
│   ├── README.md          # índice da documentação viva
│   ├── development.md     # diretriz de desenvolvimento (checklist de PR passo a passo)
│   ├── adr/               # Architecture Decision Records
│   └── features/          # registro de features planejadas e implementadas (+ specs)
├── docker-compose.yml     # orquestra api, worker, web, postgres, redis, proxy  ← a ser criado
├── docker-compose.dev.yml # overrides para desenvolvimento local  ← a ser criado
├── Caddyfile              # reverse proxy / HTTPS local  ← a ser criado
└── scripts/
    ├── deploy.sh          ← a ser criado
    └── backup.sh          ← a ser criado
```

### `openapi.yaml`

Fonte única de verdade do contrato REST. Mudanças em endpoints, payloads ou schemas de resposta são propostas **aqui primeiro**, e só depois implementadas em `api` e consumidas em `web`.

- Backend gera validação/tipos a partir dele (`oapi-codegen`).
- Frontend gera o client HTTP tipado a partir dele.
- Visualização interativa: publicar via Swagger UI (link a definir após deploy).

### `docs/`

Documentação viva do projeto. Comece pelo [`docs/README.md`](./docs/README.md),
que serve de índice para:

- [`docs/development.md`](./docs/development.md) — diretriz de desenvolvimento com
  o **checklist passo a passo** que toda implementação deve seguir antes de abrir
  PR (contrato → spec → implementar → testar → lint → docs/changelog → PR →
  merge).
- [`docs/adr/`](./docs/adr/) — Architecture Decision Records. Toda decisão que
  afeta múltiplos repositórios (escolha de stack, estratégia de sandbox,
  estratégia de autenticação) é registrada aqui **antes** de virar issue de
  implementação. Convenção e índice em [`docs/adr/README.md`](./docs/adr/README.md).
- [`docs/features/`](./docs/features/) — registro de **features planejadas e
  implementadas**, uma spec por feature. O [`docs/features/README.md`](./docs/features/README.md)
  mantém um índice com estado (`planejada`, `em-andamento`, `implementada`,
  `bloqueada`, `descartada`) e referencia as specs detalhadas. Toda feature deve
  ter uma spec antes de implementação iniciar.

### `CHANGELOG.md`

Registro de mudanças no **nível de coordenação** (contrato, ADRs, docs,
orquestração) no formato [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/).
Cada repositório de aplicação (`pecom-api`, `pecom-web`) mantém seu próprio
`CHANGELOG.md` para mudanças de código de serviço.

## Ambiente local

Pré-requisitos: Docker e Docker Compose instalados no servidor/máquina local.

```bash
mkdir pecom && cd pecom
git clone https://github.com/DharioNewbery/pecom.git
git clone https://github.com/DharioNewbery/pecom-api.git
git clone https://github.com/DharioNewbery/pecom-web.git

cd pecom
cp .env.example .env
docker compose up -d --build
```

Isso sobe: API (Go), Worker de correção, Frontend (Next.js), PostgreSQL, Redis e o reverse proxy (Caddy).

## Roadmap e gestão do projeto

O roadmap não vive em nenhum repositório de código — é gerenciado no **GitHub Project** da organização, que agrega issues de `api` e `web`. O índice detalhado e sempre atualizado de features (com estado e spec) vive em [`docs/features/README.md`](./docs/features/README.md).

Fases planejadas (visão macro — detalhes por feature em cada spec):

- **0. Fundação** — infraestrutura e ambiente local; contrato OpenAPI inicial; autenticação JWT (access + refresh) com perfil ADMIN; perfil de usuário (dados, senha, preferências, histórico e métricas pessoais); painel admin (gestão de usuários, papéis, configs, auditoria); scaffold dos repos.
- **1. Núcleo acadêmico** — matérias/disciplinas; turmas (criação, matrícula, papéis); materiais didáticos; calendário acadêmico.
- **2. Tarefas e correção** — banco de questões (global da organização, pessoal vs publicado); versionamento de questões (imutável após uso); atividades avaliativas (tarefas e testes equivalentes, com flag de peso/nota); montagem (seleção manual + sorteio por critérios com seed); correção automatizada (objetivas, V/F, associação, código via sandbox); correção manual (discursivas, envio de PDF, rubrica); pontuação e cálculo de resultado (regras configuráveis por turma).
- **3. Provas** — aplicação de provas (janela, peso, recuperação); prova segura (anti-cola, cliente dedicado fullscreen, restrição por IP/dispositivo + senha de prova; medidas configuráveis pelo professor por prova).
- **4. Métricas e dashboards** — métricas por questão (visão de professor: dificuldade/discriminação; visão de aluno: desempenho comparativo); métricas por usuário (painel do aluno; painel do professor).
- **5. Refino e hardening** — exportação de dados CSV (professor: suas turmas; admin: qualquer entidade); importação em lote (alunos, questões, turmas via CSV); notificações (email/in-app); refino e hardening.

## Convenções gerais (válidas para todos os repositórios)

- **Commits**: [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `chore:`, `refactor:`, `test:`, `docs:`).
- **Branches**: `main` protegida; desenvolvimento em `feat/nome-da-feature`.
- **Pull Requests**: obrigatórios mesmo em desenvolvimento solo. O CI precisa rodar antes do merge. Antes de abrir um, seguir o checklist passo a passo em [`docs/development.md`](./docs/development.md).
- **Segredos**: nunca commitados. Cada repositório mantém seu próprio `.env.example`.
- **Changelog**: manter `CHANGELOG.md` atualizado no repositório tocado (cada repo mantém o seu; o deste repo rastreia o nível de coordenação).

## Deploy

Deploy é feito em servidor local (self-hosted). Ver `scripts/deploy.sh` — o servidor "puxa" as atualizações (pull-based), não recebe webhooks externos.