# Cronograma

Dashboard inteligente de estudos para preparação para Residência Médica.

## Objetivo

Organizar e otimizar os estudos utilizando:

- Conteúdos novos
- Q1, Q2 e Q3
- Revisões espaçadas
- Planejamento semanal
- Histórico semanal
- Estatísticas de desempenho
- Priorização baseada em prevalência das provas

O sistema foi desenvolvido para apoiar a preparação para provas como:

- AMP
- AMRIGS
- SANAR

---

## Dashboard

Acesse:

https://alixberte.github.io/cronograma/

---

## Documentação

A documentação do projeto está na pasta:

```text
docs/
```

Arquivos principais:

- ARQUITETURA.md
- PROMPT_MASTER.md
- BUGS.md
- ROADMAP.md
- CHANGELOG.md

---

## Estrutura do Projeto

```text
cronograma/
│
├── index.html
│
├── docs/
│   ├── ARQUITETURA.md
│   ├── BUGS.md
│   ├── CHANGELOG.md
│   ├── PROMPT_MASTER.md
│   └── ROADMAP.md
│
├── data/
│   └── estado_atual.json
│
├── releases/
│
└── backups/
```

---

## Princípios do Sistema

### State Driven

O sistema é orientado a estado.

Fluxo:

```text
Estado
↓
Scheduler
↓
Cronograma
```

O cronograma nunca deve ser utilizado como fonte de verdade.

---

### Regra Pedagógica

Todo conteúdo novo gera:

```text
Conteúdo Novo
+
Q1
```

na mesma semana.

Fluxo completo:

```text
Conteúdo
↓
Q1
↓
Q2
↓
Q3
↓
Revisões
```

---

## Tempos Médios

- Conteúdo Novo = 50 min
- Q1 = 30 min
- Q2 = 30 min
- Q3 = 30 min
- Revisões = 30 min

---

## Status Atual

Projeto em desenvolvimento ativo.

Próximos focos:

- Auditoria do Scheduler
- Validation Engine
- Reconciliation Engine
- Full Rebuild Scheduler
- Melhorias na sincronização
- Otimização do backlog

---

## Autor

Projeto pessoal de gestão de estudos para Residência Médica.
