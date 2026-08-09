# Architecture Decision Records (ADR)

Architecture Decision Records (ADRs) registram **decisões técnicas relevantes**
que afetam múltiplos repositórios do projeto PECOM (ex.: escolha de stack,
estratégia de sandbox, autenticação, estratégia de cache, modelagem de dados
transversal). Decisões pontuais que vivem dentro de um único repo/módulo não
precisam de ADR aqui — registre-as no próprio repo (commit/PR description) ou em
um ADR interno a ele.

Por que manter ADRs?

- Compõem o **histórico imutável** de decisões: ainda que a decisão seja
  revertida depois, o ADR original permanece marcado como _Superseded_.
- Reposam o **contexto** que motivou a decisão — útil meses depois, quando o
  _porquê_ já não é óbvio.
- Forçam **comunicação explícita** entre frontend e backend em aspectos
  transversais.

## Convenção de formato

Cada ADR é um único arquivo Markdown nomeado `NNNN-titulo-curto-em-kebab.md`,
onde `NNNN` é um número sequencial crescente (zero-padded a 4 dígitos). Exemplo:

```
docs/adr/0001-sqlc-em-vez-de-orm.md
docs/adr/0002-sandbox-de-execucao-via-docker.md
```

Cada ADR segue este esqueleto:

```markdown
# NNNN. Título da decisão

- **Status:** Proposta | Aceita | Rejeitada | Depreciada | Substituída por NNNN
- **Data:** YYYY-MM-DD
- **Decisores:** (pessoas envolvidas)

## Contexto
Por que estamos decidindo agora? Quais forças/factos em jogo?

## Decisão
O que decidimos fazer, em uma frase.

## Consequências
O que muda. Efeitos positivos e negativos. Trade-offs aceitos.

## Alternativas consideradas
Outras opções e por que não foram escolhidas.

## Links
Issues, PRs, specs de feature, fonte de referência.
```

## Ciclo de vida

1. **Proposta** — criado como parte de uma discussão (issue/PR). Número
   reservado.
2. **Aceita** — após consenso/convergência. Em PRs subsequentes implementa-se a
   decisão.
3. **Depreciada** — se a decisão vale apenas para um período (ex.: "solução
   temporária até X").
4. **Substituída por** — usado quando um ADR mais novo a revê. O ADR antigo é
   marcado `Status: Substituída por NNNN` no topo e adiciona link para o novo.
5. **Rejeitada** — proposta que não vingou; mantida para histórico.

ADRs são **imutáveis no conteúdo histórico**. Para revisar uma decisão, crie um
novo ADR e marque o antigo como _Substituída por_ — não reescreva o ADR original.

## Quando criar um ADR

Crie um ADR quando a decisão:

- Afetar **dois ou mais** repositórios do projeto; **ou**
- Tiver **trade-offs** não-óbvios (custo, perfomance, segurança, operação); **ou**
- Tiver **impacto duradouro** difícil de desfazer (schema de banco, escolha de
  framework, protocolo de comunicação); **ou**
- For uma **convenção transversal** (naming, paginação, erros de API).

Não crie ADR para:

- Detalhes de implementação internos a um módulo (`README`/PR do próprio repo
  basta).
- Decisões triviais com reversão barata.
- Bugs e correções pontuais (use issue/PR).

## Índice

| N | Título | Status | Data |
|---|---|---|---|
| — | (nenhum ADR registrado ainda) | — | — |