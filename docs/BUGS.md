# Bugs Conhecidos

## Prioridade Alta

### Bug #001

Revisões futuras nem sempre aparecem corretamente no cronograma.

Status:
Aberto

---

### Bug #002

Ao excluir uma sessão, o assunto nem sempre retorna ao backlog.

Status:
Aberto

---

### Bug #003

O cronograma nem sempre recompõe automaticamente semanas futuras após mudanças de estado.

Status:
Aberto

---

### Bug #004

Necessário validar se Q2 e Q3 estão sendo gerados automaticamente.

Status:
Corrigido

Correções:
- `_autoMatchSessionToWeekPlan()` refatorado para aceitar `targetWkISO` e iterar todas as sessões da semana (antes matcheava apenas a última sessão registrada)
- Capacidade do blockPool corrigida: Q3 tasks usam `_mQ3` em vez de `_mQ2`

---

### Bug #005

Necessário validar se WeekHistory está realmente congelando semanas passadas.

Status:
Corrigido

Correções:
- `_archiveWeek()` chama `_autoMatchSessionToWeekPlan(wkISO)` antes de congelar (garante que sessões registradas sejam reconhecidas antes do snapshot)
- `actualHours` calculado pela soma de `task.min` das tarefas concluídas (antes usava `sp.minRev` fixo para todos os tipos)

---

## Prioridade Média

### Bug #006

Validar sincronização completa entre dispositivos.

Status:
Em análise

---

### Bug #007

Validar exportação/importação completa do JSON.

Status:
Em análise

---

## Como registrar novos bugs

Formato:

### Bug #XXX

Descrição:

Impacto:

Como reproduzir:

Status:
Aberto / Em análise / Corrigido
