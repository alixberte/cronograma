# PROMPT MASTER — PROJETO CRONOGRAMA

## VISÃO GERAL

O Projeto Cronograma é um dashboard de estudos para preparação para Residência Médica.

O sistema deve funcionar como um planejador adaptativo de estudos inspirado em ferramentas como:

- Motion
- Sunsama
- Linear

Porém adaptado para aprendizagem médica.

O foco NÃO é apenas organizar tarefas.

O foco é maximizar:

- retenção;
- revisão;
- aprendizado;
- incidência das provas;
- taxa de aprovação.

---

# PRINCÍPIO FUNDAMENTAL

O sistema deve ser totalmente State-Driven.

A fonte de verdade é:

JSON
+
Sessões Registradas
+
Questões Realizadas
+
Revisões Realizadas
+
Configurações

O cronograma deve ser derivado do estado.

Nunca o contrário.

Fluxo correto:

Estado
↓
Scheduler
↓
Cronograma

Nunca:

Cronograma
↓
Estado

---

# SOURCE OF TRUTH

A fonte oficial de dados é:

- JSON exportado pelo dashboard
- sessões registradas
- revisões registradas
- Q1 concluídos
- Q2 concluídos
- Q3 concluídos

O sistema nunca deve confiar exclusivamente:

- no cronograma renderizado;
- em snapshots antigos;
- em caches antigos;
- em weekPlans persistidos.

Sempre reconstruir a verdade a partir do estado.

---

# FILOSOFIA PEDAGÓGICA

O sistema deve respeitar o método real de estudo do usuário.

---

## Conteúdo Novo

Sempre que um conteúdo novo for estudado:

1. Aula
2. Anotações
3. Q1

Tudo na mesma semana.

Portanto:

Conteúdo Novo
+
Q1

representam uma única unidade pedagógica.

Nunca separar.

---

## Fluxo de Aprendizagem

Todo conteúdo segue:

Conteúdo
↓
Q1
↓
Q2
↓
Q3
↓
Revisões Espaçadas

O scheduler deve considerar o ciclo completo.

---

# TEMPOS MÉDIOS

Conteúdo Novo = 50 min

Q1 = 30 min

Q2 = 30 min

Q3 = 30 min

Revisão = 30 min

Todos os cálculos devem utilizar esses valores.

Os tempos devem ser configuráveis.

---

# REVISÕES ESPAÇADAS

Configuração atual:

Acerto > 90%
→ 90 dias

Acerto > 80%
→ 60 dias

Acerto > 70%
→ 45 dias

Acerto > 60%
→ 30 dias

Acerto < 60%
→ 15 dias

Esses parâmetros devem ser editáveis.

---

# PRIORIZAÇÃO

A distribuição dos assuntos deve considerar:

- prevalência AMP
- prevalência AMRIGS
- prevalência SANAR
- pesos configurados

O usuário pode alterar os pesos.

Exemplo:

AMP = 70%
AMRIGS = 30%
SANAR = 0%

O scheduler deve recalcular automaticamente.

---

# FULL REBUILD SCHEDULER

O sistema deve possuir:

fullRebuildScheduler()

Capaz de:

- invalidar cache;
- reconstruir estado;
- reconstruir revisões;
- reconstruir backlog;
- recalcular prioridades;
- recalcular cronograma;
- validar consistência.

---

# RECONCILIATION ENGINE

Ao importar JSON:

Executar:

validateSchema()

reconcileState()

rebuildTopicStates()

rebuildSpacedReviews()

rebuildBacklog()

recomputePriorities()

recomputeScheduler()

validateScheduler()

---

# VALIDATION ENGINE

O sistema deve detectar:

- revisões órfãs;
- backlog órfão;
- assunto sem semana;
- revisão sem data;
- revisão sem assunto;
- Q3 pendente já concluído;
- duplicações;
- inconsistências.

---

# WEEK PLAN

Representa planejamento.

Contém:

- tarefas planejadas;
- horas planejadas;
- questões planejadas.

---

# WEEK EXECUTION

Representa execução real.

Contém:

- tarefas concluídas;
- horas realizadas;
- questões realizadas.

---

# WEEK HISTORY

Representa semanas encerradas.

Deve ser:

- imutável;
- auditável;
- permanente.

Uma semana encerrada nunca deve ser recalculada.

---

# FECHAMENTO SEMANAL

Ao encerrar uma semana:

1. Congelar histórico.
2. Registrar execução.
3. Registrar métricas.
4. Retornar pendências ao backlog.
5. Recalcular prioridades.
6. Recalcular cronograma futuro.

---

# BACKLOG

Todo item não concluído deve retornar ao backlog.

Exemplos:

- conteúdo;
- Q1;
- Q2;
- Q3;
- revisão.

Não copiar mecanicamente para a semana seguinte.

O scheduler deve redistribuir.

---

# DISTRIBUIÇÃO INTELIGENTE

O cronograma deve equilibrar:

- Conteúdos Novos
- Q1
- Q2
- Q3
- Revisões

O objetivo é evitar:

- excesso de revisões;
- excesso de conteúdos novos;
- acúmulo futuro.

---

# SINCRONIZAÇÃO

Todas as configurações devem ser persistidas.

Devem sincronizar:

- progresso;
- cronograma;
- revisões;
- backlog;
- configurações;
- histórico semanal.

---

# NÃO CRIAR DADOS SINTÉTICOS

O sistema não deve inventar:

- revisões;
- tarefas;
- blocos;
- semanas;
- eventos.

Toda informação deve possuir origem rastreável.

---

# OBJETIVO FINAL

Criar um sistema robusto de gestão de estudos para Residência Médica.

Características desejadas:

- State Driven
- Auditável
- Adaptativo
- Escalável
- Consistente
- Reativo
- Baseado em dados

Toda implementação futura deve respeitar este documento.
