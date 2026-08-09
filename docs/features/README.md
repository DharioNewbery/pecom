# Registro de features

Este diretório registra **todas as features** planejadas e implementadas do
PECOM, com uma spec detalhada por feature. Funciona como o **catálogo de
requisitos viva** do projeto e complementa o roadmap macro (fases 0–5) descrito
no [`README.md`](../../README.md) da raiz.

Aqui vivem:

- **Specs detalhadas** de features: `docs/features/<fase-NN-nome>/<feature>.md`.
- Este `README.md` como **índice** com estado atual de cada feature.

Não vivem aqui:

- Detalhes de API (no `openapi.yaml`).
- Decisões técnicas transversais (em `docs/adr/`).
- Mudanças de código de serviço (nos próprios repos).

## Estados

Cada feature carrega um estado na primeira linha do seu arquivo (status field).
Possíveis estados:

| Estado | Significado |
|---|---|
| `planejada` | Spec escrita, ainda sem implementação. Pronta para iniciar. |
| `em-andamento` | Implementação em movimento (branch/PR ativa). |
| `implementada` | Mergeada e disponível. Não necessariamente deploiada em produção. |
| `bloqueada` | Em pausa por dependência/decisão pendente. |
| `descartada` | Decidiu-se não implementar; spec mantida para registro. |

Esse estado também é refletido no índice abaixo — mantenha os dois sincronizados.

## Convenção de pastas

Organizadas por **fase** do roadmap (cada fase é uma pasta):

```
docs/features/
├── README.md            (este arquivo — índice)
├── template.md          (template para nova spec)
├── fase-0-fundacao/
│   └── auth-jwt.md
├── fase-1-nucleo-academico/
│   └── ...
├── fase-2-tarefas-correcao/
│   └── ...
├── fase-3-provas/
│   └── ...
├── fase-4-metricas-dashboards/
│   └── ...
└── fase-5-refino-hardening/
    └── ...
```

Fases batem com as fases do roadmap no `README.md` raiz.

## Como criar uma nova feature

1. Determine a fase (0–5).
2. Copie `template.md` para `fase-NN-<nome-da-fase>/<slug-da-feature>.md`.
3. Preencha usando o `openapi.yaml` como referência para endpoints e schemas.
4. Adicione uma linha ao índice abaixo com estado inicial `planejada`.
5. Abra uma PR `docs: spec de <feature>` — a própria spec é revisada como
   qualquer outra mudança.

> Uma feature sem spec não deve ter implementação iniciada. A spec é
> pré-requisito.

## Ciclo de vida de uma feature

```
planejada (spec pronta)
    │
    ▼
em-andamento (implementando — branch feat/<slug>)
    │
    ▼
implementada (merged)         ← Move para lista de "Implementadas" abaixo
    │                           e atualiza CHANGELOG
    ▼
(livre) aplica patches / refinos conforme novas PRs
```

Se houver pausa: `bloqueada` com motivo no campo "Bloqueio". Se a feature for
abandonada: `descartada`, com motivo.

## Índice

### Planejadas

| Fase | Feature | Spec | Última atualização |
|---|---|---|---|
| 0 | Infraestrutura e ambiente local (docker-compose, Caddy, postgres, redis) | (a criar) | — |
| 0 | Contrato OpenAPI inicial + geração de client/server | (a criar) | — |
| 0 | Autenticação JWT (access + refresh) com perfil ADMIN | [`fase-0-fundacao/auth-jwt.md`](./fase-0-fundacao/auth-jwt.md) | 2026-08-09 |
| 0 | Perfil de usuário (dados, senha, preferências, histórico e métricas pessoais) | (a criar) | — |
| 0 | Painel admin (gestão de usuários, papéis, configs, auditoria) | (a criar) | — |
| 0 | Scaffold dos repos (`pecom-api`, `pecom-web`) | (a criar) | — |
| 1 | Matérias/Disciplinas (catálogo de disciplinas) | (a criar) | — |
| 1 | Turmas (criação, matrícula, papéis) | (a criar) | — |
| 1 | Materiais didáticos (upload, organização por turma) | (a criar) | — |
| 1 | Calendário acadêmico | (a criar) | — |
| 2 | Banco de questões (global da organização; pessoal vs publicado) | (a criar) | — |
| 2 | Versionamento de questões (imutável após uso; edição gera nova versão) | (a criar) | — |
| 2 | Atividades avaliativas (tarefas e testes; flag de peso/nota opcional) | (a criar) | — |
| 2 | Montagem de atividades (seleção manual + sorteio por critérios com seed) | (a criar) | — |
| 2 | Correção automatizada (objetivas, V/F, associação, código via sandbox) | (a criar) | — |
| 2 | Correção manual (discursivas, envio de PDF, rubrica) | (a criar) | — |
| 2 | Pontuação e cálculo de resultado (regras configuráveis por turma) | (a criar) | — |
| 3 | Provas (aplicação; janela, peso, recuperação) | (a criar) | — |
| 3 | Prova segura (anti-cola, cliente dedicado fullscreen, restrição IP/dispositivo+senha; configurável por prova) | (a criar) | — |
| 4 | Métricas por questão (professor: dificuldade/discriminação; aluno: desempenho comparativo) | (a criar) | — |
| 4 | Métricas por usuário (painel do aluno; painel do professor) | (a criar) | — |
| 5 | Exportação de dados CSV (professor: suas turmas; admin: qualquer entidade) | (a criar) | — |
| 5 | Importação em lote (alunos, questões, turmas via CSV) | (a criar) | — |
| 5 | Notificações (email/in-app) | (a criar) | — |
| 5 | Refino e hardening | (a criar) | — |

### Em andamento

| Fase | Feature | Spec | Branch/PR |
|---|---|---|---|
| — | (nenhuma) | — | — |

### Implementadas

| Fase | Feature | Spec | Versão do contrato | Data |
|---|---|---|---|---|
| — | (nenhuma ainda) | — | — | — |

### Bloqueadas / Descartadas

| Estado | Feature | Motivo |
|---|---|---|
| — | (nenhuma) | — |