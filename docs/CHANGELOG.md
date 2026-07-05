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

## v2.0 — Plataforma multiusuário (Firebase)

Adicionado:

- Login com e-mail/senha via Firebase Authentication (criar conta, entrar, sair, redefinir senha)
- Progresso salvo na nuvem (Firestore) vinculado ao login — documento `users/{uid}`
- Tela de login cobre o app até autenticação; contas novas começam com progresso zerado
- Cache local isolado por usuário (`qg_v5__<uid>`)
- Migração automática: dados legados (pré-login) do navegador são adotados pela primeira conta que logar
- Identidade dinâmica na sidebar e saudação (nome derivado do e-mail)
- Modal "Minha conta" com salvar/recarregar/sair

Removido:

- Backend Google Apps Script (modal de configuração, código embutido, URL no localStorage)
- Estado embutido do proprietário no HTML (dados de exemplo hardcoded)

---

## v2.1

Corrigido:

- Simulados agora fazem backup automático na nuvem (registrar/excluir marcavam apenas o localStorage)
- Merge do login é empurrado de volta ao Firestore — alterações offline sobem imediatamente ao logar
- Marcar/desmarcar tarefa da semana agora salva na nuvem em ~2s (antes só no ciclo de 30s)

Adicionado:

- Configurações de revisão espaçada (SR) e pesos de banca agora vivem no STATE (`srCfg`, `bancaCfg`) — sincronizam entre dispositivos e são isoladas por conta; localStorage vira fallback legado
- `queueCloudSave()` — caminho único de backup: dirty + debounce 2s para toda mutação de STATE

---

## v2.2

Adicionado:

- Cadastro completo: nome completo, idade (15–99) e período (1º a 12º)
- Tela de login com dois modos — "Entrar" e "Criar conta" (campos extras aparecem só no cadastro)
- Perfil gravado em `users/{uid}.profile` no Firestore e `displayName` no Firebase Auth
- Sidebar mostra nome real, iniciais no avatar e "Xº período · Y anos"
- Saudação da página Hoje usa o nome real do usuário

Corrigido:

- `syncSave` usa `{merge:true}` — backup do STATE não apaga mais o campo profile do documento

---

## v2.3

Adicionado:

- Edição de perfil no modal "Minha conta": nome, idade e período editáveis a qualquer momento
- Mesmas validações do cadastro; feedback com toast e mensagens de erro no próprio modal
- Alterações refletem imediatamente na sidebar, avatar e saudação, e sincronizam no Firestore

---

## v2.4

Adicionado:

- Snapshots semanais automáticos do progresso em `users/{uid}/backups/{segundaISO}` — criados no primeiro salvamento de cada semana, retenção dos 12 mais recentes
- Seção "🕘 Backups semanais" no modal Minha conta: lista snapshots e permite restaurar qualquer um
- Restauração segura: um backup "pre-restore" do estado atual é criado antes de substituir (máx. 3 retidos)

Atenção (infra):

- Requer atualização das regras do Firestore para cobrir subcoleções: `match /users/{uid}/{document=**}`

---

## v2.5

Corrigido:

- Migração de dados legados não é mais silenciosa: ao logar uma conta sem dados na nuvem num navegador com progresso antigo, o app PERGUNTA se deve importar — contas de teste não absorvem mais os dados do proprietário
- Recusa é lembrada por conta (`qg_legacy_declined_<uid>`); os dados antigos nunca são apagados
- Marcador global `qg_legacy_adopted` abandonado — a conta real do proprietário recebe a oferta de importação mesmo que outra conta tenha logado antes

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
