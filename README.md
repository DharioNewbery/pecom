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
├── openapi.yaml          # contrato único da API — fonte de verdade para backend e frontend
├── docs/
│   └── adr/               # Architecture Decision Records
├── docker-compose.yml     # orquestra api, worker, web, postgres, redis, proxy
├── docker-compose.dev.yml # overrides para desenvolvimento local
├── Caddyfile              # reverse proxy / HTTPS local
└── scripts/
    ├── deploy.sh
    └── backup.sh
```

### `openapi.yaml`

Fonte única de verdade do contrato REST. Mudanças em endpoints, payloads ou schemas de resposta são propostas **aqui primeiro**, e só depois implementadas em `api` e consumidas em `web`.

- Backend gera validação/tipos a partir dele (`oapi-codegen`).
- Frontend gera o client HTTP tipado a partir dele.
- Visualização interativa: publicar via Swagger UI (link a definir após deploy).

### `docs/adr/`

Registros de decisões técnicas relevantes, no formato:

```
docs/adr/0001-sqlc-em-vez-de-orm.md
docs/adr/0002-sandbox-de-execucao-via-docker.md
```

Toda decisão que afeta múltiplos repositórios (escolha de stack, estratégia de sandbox, estratégia de autenticação) deve ser documentada aqui antes de virar issue de implementação.

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

O roadmap não vive em nenhum repositório de código — é gerenciado no **GitHub Project** da organização, que agrega issues de `api` e `web`.

Fases planejadas (visão macro — detalhes em cada issue/milestone):

0. Fundação (infraestrutura e autenticação)
1. Núcleo acadêmico (turmas, materiais, calendário)
2. Tarefas e correção automatizada
3. Provas
4. Métricas e dashboards
5. Refino e hardening

## Convenções gerais (válidas para todos os repositórios)

- **Commits**: [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `chore:`, `refactor:`, `test:`, `docs:`).
- **Branches**: `main` protegida; desenvolvimento em `feat/nome-da-feature`.
- **Pull Requests**: obrigatórios mesmo em desenvolvimento solo. O CI precisa rodar antes do merge.
- **Segredos**: nunca commitados. Cada repositório mantém seu próprio `.env.example`.

## Deploy

Deploy é feito em servidor local (self-hosted). Ver `scripts/deploy.sh` — o servidor "puxa" as atualizações (pull-based), não recebe webhooks externos.