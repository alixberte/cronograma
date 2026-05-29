# Projeto Cronograma

## Objetivo

Dashboard de estudos para preparação para Residência Médica.

O sistema deve organizar:

- Conteúdos novos
- Questões (Q1, Q2, Q3)
- Revisões espaçadas
- Histórico semanal
- Estatísticas
- Planejamento até a prova

O objetivo principal é maximizar aprovação nas provas AMP e AMRIGS utilizando um cronograma adaptativo baseado em prevalência dos assuntos.

---

# Filosofia do Sistema

O sistema deve ser totalmente State-Driven.

O cronograma NÃO é a fonte de verdade.

A fonte de verdade é:

JSON
+
Sessões registradas
+
Q1/Q2/Q3 concluídos
+
Revisões concluídas
+
Configurações do usuário

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

# Estruturas Principais

## Topic

Representa um assunto.

Exemplos:

- Reanimação Neonatal
- Queimaduras
- Sepse
- AVC

Cada Topic possui:

- especialidade
- prevalência
- prioridade
- status
- histórico

---

## Review

Representa uma revisão.

Tipos:

- Espaçada
- Atrasada
- Pendente

---

## WeekPlan

Planejamento teórico da semana.

Contém:

- tarefas planejadas
- horas planejadas
- questões planejadas

---

## WeekExecution

Execução real da semana.

Contém:

- tarefas concluídas
- horas realizadas
- questões realizadas

---

## WeekHistory

Histórico congelado de semanas encerradas.

Semanas encerradas nunca devem ser modificadas.

---

## Scheduler

Responsável por:

- distribuir conteúdos
- distribuir Q1
- distribuir Q2
- distribuir Q3
- distribuir revisões
- rebalancear cronograma

---

# Regras Pedagógicas

## Conteúdo Novo

Todo conteúdo novo gera:

Conteúdo + Q1

na mesma semana.

Nunca separar.

---

## Questões

Q1 = mesma semana do conteúdo

Q2 = programado automaticamente

Q3 = programado automaticamente

---

## Revisões

As revisões seguem o percentual de acerto.

Configuração atual:

>90% = 90 dias

>80% = 60 dias

>70% = 45 dias

>60% = 30 dias

<60% = 15 dias

---

# Tempos Médios

Conteúdo Novo = 50 min

Q1 = 30 min

Q2 = 30 min

Q3 = 30 min

Revisão = 30 min

---

# Priorização

Utilizar:

- Prevalência AMP
- Prevalência AMRIGS
- Pesos configurados pelo usuário

Exemplo:

AMP = 70%
AMRIGS = 30%

O scheduler deve recalcular automaticamente.

---

# Objetivo Arquitetural

O sistema deve funcionar como um scheduler adaptativo semelhante a:

- Motion
- Sunsama
- Linear

Com histórico imutável e planejamento futuro recalculável.
