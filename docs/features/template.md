<!--
ESTADO: planejada
FASE: 0 — Fundação
FEATURE: <slug-da-feature>
TÍTULO: <título legível>
-->

# <Título da feature>

> **Estado:** `planejada` | `em-andamento` | `implementada` | `bloqueada` | `descartada`
> **Fase:** 0 — Fundação
> **Versão do contrato alvo:** `0.1.0` (p.ex.)
> **Data da última atualização:** YYYY-MM-DD
> **Responsável:** _(pessoa ou "a definir")_
> **Issue de implementação:** #_(opcional)_

## Resumo

Uma frase descrição: o quê é a feature e qual valor entrega a professores e/ou
alunos.

## Motivação

Por quê precisamos desta feature? Qual problema resolve? Quem impacta?

## Escopo

### Em escopo
- _(lista do que está incluído nesta feature)_

### Fora de escopo
- _(explicitly fora; funcionalidades próximas que NÃO serão tocadas agora)_

## User stories

Como **<persona>** (professor | aluno | admin), quero **<ação>** para que
**<valor>**.

- US1: ...
- US2: ...

## Endpoints / Schemas (contrato)

Listar endpoints envolvidos, com método e path. Esta lista deve **refletir
exatamente** o `openapi.yaml` (e a spec é a fonte de discussão, mas a
implementação segue o contrato finalizado).

- `POST /api/v1/...` — descrição
- `GET /api/v1/...` — descrição
- Schemas envolvidos: `X`, `Y` (definidos em `openapi.yaml#/components/schemas/`)

Se a feature não envolver API (ex.: puramente local), explicitar:
> Sem novos endpoints. Apenas <componente/alvo>.

## Regras de negócio

1. ...
2. ...

## Requisitos não-funcionais

- Segurança: ...
- Performance: ...
- Observabilidade: métricas/logs a adicionar.
- Internacionalização: ...

## Critérios de aceite

- [ ] Critério verificável 1.
- [ ] Critério 2 — com referência ao endpoint/schema.
- [ ] ...

## Dependências e riscos

- Depende de: _(outra feature, contrato em versão X, infra/disponibilidade)_
- Riscos: ...
- Mitigação: ...

## Notas de implementação

Apenas preenchido quando `em-andamento`/`implementada`. Conta detalhes
relevantes que surgiram durante a implementação (ex.: workaround, trade-off
descoberto). ADRs criados devem ser linkados aqui.

## Checklist de implementação

(ver [`docs/development.md`](../development.md) para procedimento completo)

- [ ] Contrato atualizado em `pecom/openapi.yaml` (bump de versão).
- [ ] Implementado em `pecom-api` (`handler` → `service` → `repository`).
- [ ] Implementado em `pecom-web` (`generate:api` rodado, hooks/telas criadas).
- [ ] Migrations criadas e reversíveis (se aplicável).
- [ ] Testes unitários em `service`; testes de integração para endpoints
      críticos.
- [ ] `go test ./...`, `golangci-lint run`, `npm test`, `npm run lint`,
      `npm run typecheck` verdes.
- [ ] CHANGELOG atualizado nos repos tocados.
- [ ] Estado da feature mudado para `implementada` no índice de features.

## Links

- ADR(s): _(nenhum ainda)_
- PRs: _(nenhuma ainda)_
- Issues: _(nenhuma ainda)_