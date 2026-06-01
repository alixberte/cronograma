# Changelog

## v1.0

Criação inicial do dashboard.

---

## v1.1

Implementação:

- Cronograma semanal
- Registro de sessões

---

## v1.2

Implementação:

- Revisões espaçadas
- Controle de Q1/Q2/Q3

---

## v1.3

Implementação:

- Configurações de revisão
- Configurações de pesos AMP/AMRIGS/SANAR

---

## v1.4

Implementação:

- Histórico semanal
- WeekHistory

---

## v1.5

Implementação:

- Exportação JSON
- Importação JSON
- Sincronização

---

## v1.6

Corrigido:

- PLANILHA e DISC_WEIGHTS extraídos para `planilha.js` (script Python pode regenerar sem tocar no HTML)
- `computeTopic()`: double-counting de q/a eliminado — usa exclusivamente STATE.sessions quando há sessões com questões
- `validateScheduler()` conectado ao ciclo de vida: chamado no startup, em `fullRebuildScheduler()` e sob demanda
- Painel "Diagnóstico do sistema" adicionado em Configurações

Adicionado:

- `_lastValidation[]` — variável global persiste resultado da validação entre chamadas

---

## v1.7

Corrigido:

- `_autoMatchSessionToWeekPlan()`: iterava apenas a última sessão registrada — agora itera todas as sessões da semana alvo
- `_autoMatchSessionToWeekPlan()`: aceita `targetWkISO` opcional para matchear semanas passadas antes do fechamento
- `_archiveWeek()`: chama `_autoMatchSessionToWeekPlan(wkISO)` antes de congelar o snapshot
- `_archiveWeek()`: `actualHours` calculado pela soma real de `task.min` (antes usava `sp.minRev` fixo para todos os tipos)
- Scheduler blockPool: Q3 tasks usam `_mQ3` para cálculo de capacidade (antes usava `_mQ2`)

---

## v1.8

Corrigido:

- URL privada do Google Apps Script removida do código-fonte (estava exposta em 2 locais)
- Cache key de `computeAll()` agora inclui schedulerParams e BANCA_CFG — invalida corretamente ao mudar pesos de banca ou intervalos SR
- Startup executava `recomputeScheduler()` antes do monkey-patch BANCA_CFG, causando primeiro render com prioridades sem ponderação de banca — corrigido com re-render pós-patch

---

## v1.9

Corrigido:

- Scheduler: pool `upcomingRevs` expandido — revisões futuras além de 7 dias agora entram no cronograma nas semanas corretas (Bug #001)
- `delSess()`: entradas `auto_match` em `weekExecution` são limpas ao excluir sessão e reavaliadas (Bug #002)
- `doImportJSON()`: validação de schema adicionada antes de substituir STATE — verifica presença de `sessions` e versão compatível (v4/v5)
- `doImportJSON()`: `alert()` substituído por toast + reload suave
- `_archiveWeek()`: `weekPlans` e `weekExecution` da semana arquivada são purgados do STATE após criar weekHistory (Bug #003)

---

## Próxima Versão

Planejado:

- Full Rebuild Scheduler
- Melhorias de Backlog

---

# Convenção

Ao finalizar qualquer alteração:

Adicionar nova versão.

Exemplo:

## v1.6

Alterado:

- ...

Corrigido:

- ...

Adicionado:

- ...
