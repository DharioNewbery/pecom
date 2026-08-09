# Autenticação JWT (access + refresh) com perfil ADMIN

> **Estado:** `planejada`
> **Fase:** 0 — Fundação
> **Versão do contrato alvo:** `0.1.0`
> **Data da última atualização:** 2026-08-09
> **Responsável:** a definir
> **Issue de implementação:** _(opcional)_

## Resumo

Sistema de autenticação baseado em JWT com **access token** de curta duração e
**refresh token** de longa duração, permitindo que professores e alunos façam
login, tenham sessões revogáveis e renovem access tokens sem relogar.

## Motivação

Toda feature subsequente (turmas, tarefas, submissões, métricas) depende de
identidade e autorização por perfil. A autenticação é o **alicerce** da Fase 0:
sem ela, nenhum endpoint não-público pode ser protegido.

## Escopo

### Em escopo
- Cadastro de usuários (professor, aluno e admin).
- Login com email + senha (verificação de senha com hash).
- Emissão de **access token** (JWT, curta duração) e **refresh token** (JWT
  opaco ou JWT com claim própria, longa duração).
- Endpoint de **refresh** para renovar access token a partir de refresh token
  válido.
- Endpoint de **logout** que revoga o refresh token.
- Middleware de autenticação e autorização por **perfil** (professor/aluno/admin).
- Rotação simples de refresh tokens (single-use) com detecção de reuso.

### Fora de escopo
- SSO / OAuth2 / login social (futuro).
- 2FA / TOTP (futuro).
- Recuperação de senha por email (feature separada dentro da Fase 0).
- Verificação de email (feature separada).
- CRUD administrativo de usuários/papéis/configs/auditoria — objeto da feature
  "Painel admin" (Fase 0), que depende desta feature para existir.

## User stories

- Como **professor**, quero me cadastrar e logar para acessar funcionalidades
  restritas ao meu perfil.
- Como **aluno**, quero me cadastrar e logar para acessar minhas turmas e
  tarefas.
- Como **admin**, quero logar para administrar usuários, papéis, configurações
  e auditar o sistema (a gestão em si é objeto da feature "Painel admin").
- Como **usuário logado**, quero renovar meu access token sem refazer login
  quando ele expirar.
- Como **usuário logado**, quero fazer logout e invalidar minha sessão em outros
  dispositivos.
- Como **sistema**, quero que um refresh token reutilizado invalide toda a
  "sessão" associada (detecção de roubo).

## Endpoints / Schemas (contrato)

Versão alvo do contrato: `0.1.0`. Endpoints a definir em `openapi.yaml`
quando o contrato for criado:

- `POST /api/v1/auth/registro` — cria um usuário; retorna tokens.
- `POST /api/v1/auth/login` — autentica e retorna access + refresh.
- `POST /api/v1/auth/refresh` — troca refresh por novo access (+ novo refresh).
- `POST /api/v1/auth/logout` — revoga refresh token atual.
- `GET /api/v1/auth/me` — retorna perfil do usuário autenticado.

Schemas envolvidos (a definir em `openapi.yaml#/components/schemas/`):

- `RegistroRequest`, `LoginRequest`, `AuthResponse`, `RefreshRequest`,
  `RefreshResponse`, `Usuario`, `Perfil` (`PROFESSOR` | `ALUNO` | `ADMIN`).
- Erro padronizado: `ErroResponse`.

## Regras de negócio

1. **Senha** armazenada como hash com `bcrypt` (cost ajustável, default 12).
2. **Access token** JWT: TTL curto (15 min), assinado com `HS256` (simétrico
   nesta fase; migrar para `RS256` em fase posterior — ver ADR se decidido).
3. **Refresh token** JWT opaco: TTL longo (7 dias, configurável), com `jti`
   único; armazenar hash do `jti` em banco/Redis para permitir revogação.
4. **Rotação single-use**: cada uso de refresh emite um novo refresh e invalida
   o anterior. Reuso de refresh já utilizado → revoga **todos** refresh
   tokens da "cadeia" (cold logout, exige relogar).
5. **Perfil** verificado em cada rota protegida via middleware; rotas em
   `(professor)` exigem `perfil=PROFESSOR`, em `(aluno)` exigem
   `perfil=ALUNO|PROFESSOR` (ou conforme especificado por rota); rotas em
   `(admin)` exigem `perfil=ADMIN`. O cadastro de usuário **ADMIN** é protegido:
   apenas um ADMIN existente (ou bootstrap inicial via seed) pode criar outros
   ADMINs — detalhes do CRUD de usuários/papéis ficam na feature "Painel admin".
6. **Logout** revoga apenas o refresh informado (não força logout de outros
   dispositivos, exceto se tamam cadência special).
7. **Email** único por usuário (constraint no banco).

## Requisitos não-funcionais

- **Segurança**: senha nunca logada; tokens não logados em plaintext; refresh
  token transmitido apenas via `httpOnly` cookie OU no body da resposta (a
  decidir em ADR — recommend cookie `Secure; HttpOnly; SameSite=Strict` para
  web).
- **Performance**: verificação de JWT < 1 ms típico (HMAC local sem round-trip
  em cada request).
- **Observabilidade**: counter de logins bem/mal sucedidos; counter de
  refresh tokens reusados (sinal de segurança); logs de nível de acesso
  contendo `user_id` e `perfil` (não contendo tokens).
- **Configuração**: `<PREFIX>_JWT_ACCESS_SECRET`, `<PREFIX>_JWT_REFRESH_SECRET`,
  TTLs configuráveis via env.

## Critérios de aceite

- [ ] `POST /api/v1/auth/registro` cria usuário e retorna access + refresh.
      O registro de perfil ADMIN é restrito a um ADMIN existente (ou seed).
- [ ] `POST /api/v1/auth/login` retorna access + refresh; senha inválida retorna
      `401`.
- [ ] `POST /api/v1/auth/refresh` valida refresh, invalida o anterior e retorna
      novos tokens.
- [ ] Reuso de refresh já utilizado retorna `401` e invalida a cadeia.
- [ ] `POST /api/v1/auth/logout` revoga refresh; chamadas subsequentes com
      aquele refresh retornam `401`.
- [ ] `GET /api/v1/auth/me` retorna dados do usuário autenticado; sem
      `Authorization` retorna `401`.
- [ ] Rotas protegidas rejeitam access token expirado com `401` e permitem
      refresh via client.
- [ ] Rotas `(admin)` rejeitam `PROFESSOR`/`ALUNO` com `403`.
- [ ] Frontend armazena tokens conforme ADR de storage; renovações
      transparentes via interceptor.
- [ ] Testes de integração cubrindo: registro, login, refresh, reuso, logout,
      acesso protegido com token expirado.

## Dependências e riscos

- Depende de: `openapi.yaml` v0.1.0; `pecom-api` scaffold definido;
  `pecom-web` scaffold definido; `docker-compose.yml` rodando postgres +
  redis; `sessions` store definido (memória ou Redis).
- Riscos:
  - **Storage de tokens no frontend** (XSS vs CSRF) — mitigado por ADR
    definindo estratégia.
  - **Migração HMAC → RS256** — deixar algoritmo configurável; não
    comprometer o contrato.
  - **Reuso em ambiente NAT** — risco de falsos positivos por NAT
    compartilhada; mitigado por usar `jti` e não IP.
- Mitigação documentada em ADR(s).

## Notas de implementação

> (preencher durante a implementação)

## Checklist de implementação

(ver [`docs/development.md`](../../development.md) para procedimento completo)

- [ ] Contrato atualizado em `pecom/openapi.yaml` (v0.1.0).
- [ ] ADR criado para: estratégia de assinatura do JWT (HS256 vs RS256); para
      storage de tokens no frontend (cookie `httpOnly` vs `localStorage`).
- [ ] Implementado em `pecom-api` no módulo `internal/usuario` (+ `auth/`
      handlers/middleware conforme estrutura final).
- [ ] Implementado em `pecom-web` no grupo de rotas `(auth)` (login, registro,
      entrada + perfil).
- [ ] Migrations reversíveis para tabela `usuarios` e `refresh_tokens`.
- [ ] Testes unitários em `service`; testes de integração dos 5 endpoints.
- [ ] `golangci-lint`, `go test`, `npm run lint`, `npm test`, `npm run
      typecheck` verdes.
- [ ] CHANGELOG atualizado nos repos tocados.
- [ ] Estado da feature mudado para `implementada` no índice em
      [`docs/features/README.md`](../README.md).

## Links

- ADR(s): _(a criar — assinatura JWT; storage de tokens no frontend)_
- PRs: _(nenhuma ainda)_
- Issues: _(nenhuma ainda)_