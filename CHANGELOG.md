# Changelog

Todas as mudanças notáveis **no nível de coordenação do projeto** (contrato de API, ADRs, orquestração local, diretrizes e documentação) são registradas neste arquivo.

Cada repositório de aplicação (`pecom-api`, `pecom-web`) mantém seu próprio `CHANGELOG.md` para mudanças de código de serviço. Este arquivo acompanha o projeto de forma macro: versões do contrato, decisões de arquitetura relevantes e evolução da documentação de coordenação.

O formato é baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/),
e este projeto adere a [Semantic Versioning](https://semver.org/lang/pt-BR/) para o
contrato de API (`openapi.yaml`). O histórico de versionamento do contrato é mantido
no próprio `openapi.yaml` (cabeçalho `info.version`).

## [Unreleased]

### Adicionado
- Estrutura inicial de documentação do repositório de coordenação (`pecom`).
  - `docs/` com índice de documentação, diretriz de desenvolvimento e convenção de ADRs.
  - `docs/features/` para registro de features planejadas e implementadas, com template.
  - Spec detalhada da feature `auth-jwt` (Fase 0 — Fundação).
- `CHANGELOG.md` (este arquivo).
- Itens ao índice de features (`docs/features/README.md`) cobrindo o escopo completo da aplicação final:
  - Fase 0: perfil de usuário; painel admin (gestão de usuários, papéis, configs, auditoria).
  - Fase 1: matérias/disciplinas (distinto de materiais didáticos); materiais didáticos; calendário acadêmico.
  - Fase 2: banco de questões (global da organização; pessoal vs publicado); versionamento de questões; atividades avaliativas (tarefas e testes equivalentes); montagem (manual + sorteio com seed); correção automatizada (objetivas, V/F, associação, código via sandbox); correção manual (discursivas, envio de PDF, rubrica); pontuação e cálculo de resultado (regras configuráveis por turma).
  - Fase 3: prova segura (anti-cola + cliente dedicado fullscreen + restrição IP/dispositivo + senha; configurável por prova).
  - Fase 4: métricas por questão (professor + aluno); métricas por usuário (painel do aluno + painel do professor).
  - Fase 5: exportação CSV (professor: suas turmas; admin: qualquer entidade); importação em lote; notificações (email/in-app).
- Detalhamento das fases 0–5 no `README.md` raiz (roadmap macro).

### Alterado
- `README.md` atualizado para refletir a nova estrutura de pastas e apontar para `docs/`.
- `auth-jwt.md`: inclui perfil `ADMIN` (no enum `Perfil`, em user stories, regras de autorização para rotas `(admin)` e critérios de aceite); data corrigida para 2026-08-09; título ajustado para "Autenticação JWT (access + refresh) com perfil ADMIN". CRUD administrativo de usuários/papéis fica fora de escopo, a cargo da feature "Painel admin".
- `docs/features/README.md`: índice "Planejadas" reescrito com o escopo completo da aplicação final.

### Removido
- _Nada ainda._

## Histórico de versionamento do contrato

O `openapi.yaml` ainda não existe neste momento (Fase 0 pendente). Assim que criado,
cada release do contrato será registrada abaixo com um link para a tag correspondente.

| Versão | Descrição | Data | Tag |
|---|---|---|---|
| — | (pendente) | — | — |