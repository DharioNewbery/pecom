# Diretriz de desenvolvimento

Este documento define **como uma implementação deve ser preparada antes de abrir
um Pull Request**. O checklist foi pensado para um projeto **multi-repo com
contrato-first**: mudanças podem envolver um, dois ou os três repositórios
(`pecom`, `pecom-api`, `pecom-web`). Quanto mais repos envolvidos, mais passos
aplicam — use o bom senso.

A regra de ouro: **o contrato (`openapi.yaml`) é a fonte de verdade**. Toda
mudança de API é proposta primeiro em `pecom` (no contrato, eventualmente em um
ADR), e só depois implementada em `pecom-api` e consumida em `pecom-web`.

---

## 1. Pré-requisitos (uma vez por repositório)

- [ ] Repos `pecom`, `pecom-api` e `pecom-web` clonados lado a lado.
- [ ] `docker compose up -d --build` (a partir de `pecom/`) funcionando.
- [ ] Ferramentas de desenvolvimento disponíveis localmente:
  `go`, `golangci-lint`, `golang-migrate`, `sqlc`, `oapi-codegen`, `node`/`npm`.

---

## 2. Crie a branch

- Sempre a partir de `main` atualizada em todos os repos relevantes.
- Convenção de nome: `feat/nome-da-feature`, `fix/breve-descricao`,
  `refactor/escopo`, `docs/escopo`, `chore/escopo`.
- Se a feature tiver uma spec em `docs/features/<feature>.md`, use o mesmo slug
  no nome da branch (`feat/auth-jwt`).

```bash
git checkout main && git pull
git checkout -b feat/nome-da-feature
```

---

## 3. Contrato e decisões (se aplicável)

Se a PR altera endpoints, payloads, status codes ou schemas de resposta:

1. [ ] Atualizar `pecom/openapi.yaml` (bump `info.version` conforme SemVer:
   - MAJOR: quebra de contrato
   - MINOR: adição retrocompatível
   - PATCH: correção retrocompatível)
2. [ ] Atualizar a spec da feature em `pecom/docs/features/<feature>.md`
   (estado muda para `em-andamento`; descrição de endpoints/schemas alinhada ao
   contrato).
3. [ ] Se a mudança introduz uma decisão técnica relevante de múltiplos repos,
   criar um ADR em `pecom/docs/adr/NNNN-titulo.md` (ver
   [`adr/README.md`](./adr/README.md)).
4. [ ] Atualizar `pecom/CHANGELOG.md` se houve mudança no nível de coordenação.

A PR de `pecom` deve ser **aprovada e mergeada antes** (ou em paralelo, com
 cuidado) das PRs de `pecom-api`/`pecom-web` — porque a implementação depende do
 contrato finalizado.

---

## 4. Implementação (no repo de aplicação)

Padrões do backend (`pecom-api`) e frontend (`pecom-web`) — ver cada `README.md`.

### Backend (`pecom-api`)

- [ ] Para cada módulo envolvido: `handler.go` → `service.go` → `repository.go`
      → `model.go`. Interfaces de `Repository` definidas para permitir mocks.
- [ ] Nova query SQL? Adicionar em `db/queries/*.sql` e **regenerar sqlc**.
- [ ] Nova migration em `db/migrations/` (sempre reversível; testar `up` e `down`).
- [ ] Erros tratados explicitamente; respostas de erro no formato padronizado.
- [ ] Paginação padronizada em qualquer nova listagem (`?page=&limit=`).

### Frontend (`pecom-web`)

- [ ] Client HTTP **tipado** consumido — nunca `fetch` direto nas telas.
- [ ] Após mudança no contrato, rodar `npm run generate:api`.
- [ ] Componentes de domínio reutilizáveis em `components/shared`; UI genérica em
      `components/ui`.
- [ ] Validação de formulários com Zod, espelhando schemas do contrato.
- [ ] Hooks de React Query em `lib/hooks`; cache keys organizadas por domínio.

---

## 5. Testes

### Backend
- [ ] Testes unitários na camada de `service` para toda regra de negócio nova.
- [ ] Testes de integração para endpoints críticos (auth, submissão, correção).
- [ ] `go test ./...` verde. Cobertura dos caminhos críticos no mínimo.
- [ ] Rodar localmente contra Postgres/Redis reais via `docker compose`.

### Frontend
- [ ] Testes de componentes de domínio e hooks.
- [ ] `npm test` verde.

### Orquestração
- [ ] Se houve mudança em `openapi.yaml`, validar que backend e frontend geram
      artifacts compatíveis (`oapi-codegen` e `generate:api`).

---

## 6. Lint e typecheck

Antes de commitar, rodar em todos os repos tocados:

```bash
# pecom-api
golangci-lint run
go vet ./...

# pecom-web
npm run lint
npm run typecheck
```

CI reprova PR se algum destes falhar.

---

## 7. Documentação

- [ ] Doc da feature: atualizar estado. Mover de `planejada` → `em-andamento`
      → `implementada`. Incluir notes de implementação se relevantes.
- [ ] Doc da feature: listar nomes das apis consumidas com versão do contrato.
- [ ] ADR criado se houve decisão técnica (mesmo que tenha sido debatida em PR).
- [ ] `CHANGELOG.md` do repo tocado atualizado (cada repo mantém o seu).
- [ ] `README.md` do repo tocado se a feature envolver novo fluxo/config.
- [ ] Swagger (`/docs`) da API deu swell? `swaggo` regenerado se metadados novos.

---

## 8. Self-review

Revise sua própria diff como se fosse um revisor externo:

- [ ] Erros tratados, sem panics soltos, sem `TODO` sem contexto.
- [ ] Logs não expõem segredos/tokens.
- [ ] Variáveis de ambiente documentadas no `.env.example`.
- [ ] Sem segredos commitados (verificar `git diff` por `password`, `key`,
      tokens; se necessário, girar).
- [ ] Sem mudanças acidentais (build artifacts, `.env`, `node_modules`).
      Arquivos gerados por sqlc/openapi client **devem** ser commitados, mas
      conflitos de merge em gerados não devem entrar no commit.
- [ ] Diff mínimo e coerente; separar refactors de features em PRs distintas
      quando possível.

---

## 9. Commit e Push

- [ ] Commits seguindo [Conventional Commits](https://www.conventionalcommits.org/):
  `feat:`, `fix:`, `refactor:`, `test:`, `chore:`, `docs:`.
- [ ] Um commit por unidade lógica; squash quando fizer sentido no PR.
- [ ] `git push origin feat/nome-da-feature`.

> Não realize `git commit --amend` após push sem combinar, nem force-push em
> `main`.

---

## 10. Pull Request

Use o template de PR (configurar no GitHub; ver abaixo). Mínimo:

1. **O que** muda e **por quê** (ligação para a issue/spec da feature).
2. Lista de endpoints/schemas afetados (link para `openapi.yaml` na tag/branch).
3. Como testar manualmente.
4. Screenshots/.logs quando relevante.
5. Checklist:
   - [ ] Contrato atualizado e bumped em `openapi.yaml`.
   - [ ] Spec da feature atualizada (`docs/features/`).
   - [ ] ADR criado (se decisão técnica).
   - [ ] CHANGELOG atualizado no(s) repo(s) tocado(s).
   - [ ] Testes novos/ajustados e `go test`/`npm test` verdes.
   - [ ] Lint/typecheck verdes.
   - [ ] Migrations reversíveis e testadas (`up` e `down`).
   - [ ] Sem segredos commitados.
   - [ ] CI verde.

### Template de PR sugerido

```markdown
## Resumo
<!-- 1-3 frases: o que e por quê -->

Feature: pecom/docs/features/<feature>.md
Contrato: <link para openapi.yaml na branch>
Issue: #<n>

## Endpoints / Schemas afetados
- `POST /api/v1/...`

## Como testar
1. `docker compose up -d --build`
2. ...

## Checklist
- [ ] Contrato atualizado
- [ ] Spec da feature atualizada
- [ ] Testes
- [ ] Lint/typecheck
- [ ] Migrations reversíveis
- [ ] Sem segredos
- [ ] CI verde
```

---

## 11. Merge e pós-merge

- [ ] Squash-merge preferido (preserva Conventional Commit no título do commit
      final). Rebase-merge permitido para PRs com commits bem organizados.
- [ ] Após merge: atualizar estado da feature para `implementada` em
      `docs/features/<feature>.md` (numa PR de `docs: pós-merge` ou direto no
      mesmo PR).
- [ ] Atualizar `CHANGELOG.md` com o item na seção **Unreleased** (ou nova
      versão ao liberar um release).
- [ ] Fechar issue vinculada.
- [ ] Se feature libera próxima fase do roadmap, abrir issue para a próxima.

---

## Resumo executivo (colar no topo do seu monitor)

```
Contrato → Spec → Implementar → sqlc/generate:api → Testar → Lint → Docs/CHANGELOG → Self-review → Commit → PR → CI → Merge → Atualiza estado da feature
```