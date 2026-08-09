# Documentação

Este diretório concentra toda a documentação do projeto PECOM. O repositório `pecom` não contém código de aplicação; portanto, tudo
que mora aqui é **contrato, decisão, diretriz ou registro de feature**.

## Índice

| Caminho | Conteúdo |
|---|---|
| [`development.md`](./development.md) | Diretriz de desenvolvimento e checklist passo a passo de PR |
| [`adr/`](./adr/) | Architecture Decision Records (decisões técnicas relevantes) |
| [`features/`](./features/) | Registro de features planejadas e implementadas (e specs detalhadas) |

Documentos que vivem **fora** de `docs/` mas fazem parte da coordenação:

| Arquivo | Conteúdo |
|---|---|
| [`/openapi.yaml`](../openapi.yaml) | Contrato único da API (fonte de verdade) — _ainda a ser criado_ |
| [`/docker-compose.yml`](../docker-compose.yml) | Orquestração local — _ainda a ser criado_ |
| [`/CHANGELOG.md`](../CHANGELOG.md) | Registro de mudanças do nível de coordenação |

## Princípios

1. **Contrato primeiro.** Toda mudança que afete endpoints/payloads começa em
   `openapi.yaml`. Especificações de feature em `docs/features/` referenciam
   exatamente os endpoints e schemas do contrato — nunca o contrário.

2. **Decisões são registradas.** Escolhas que afetam múltiplos repositórios
   (stack, sandbox, autenticação, estratégia de cache) viram um ADR em
   `docs/adr/NNNN-titulo.md` **antes** de virar issue de implementação.

3. **Features têm estado.** Cada feature em `docs/features/` carrega um estado
   (`planejada`, `em-andamento`, `implementada` ou `bloqueada`). O estado é
   atualizado ao longo do ciclo de vida — ver [`features/README.md`](./features/README.md).

4. **Documentação é testada.** Toda PR que afeta o contrato, decisões de
   arquitetura ou features deve atualizar o documento correspondente. O checklist
   de PR em [`development.md`](./development.md) inclui isso explicitamente.

## Convenções de formatação

- Markdown puro (GitHub-flavored).
- Idioma: português do Brasil nos textos descritivos; termos técnicos (endpoints,
  nomes de schema, bibliotecas) mantêm o original em inglês.
- Tests de links relativos: ao mover arquivos, atualizar referências cruzadas.