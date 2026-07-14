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

## v2.6

Corrigido:

- Conta nova agora começa 100% zerada: `computeTopic()` ignora os campos pessoais da PLANILHA (stage, q, a, pct, r1–r3, herdados da planilha Python do fundador) — todo progresso deriva exclusivamente das sessões do usuário logado
- Histórico do fundador preservado: conversor `_planilhaBaselineSessions()` transforma o baseline da planilha em sessões reais (229 registros de 69 tópicos), importadas automaticamente 1x apenas nas contas em `OWNER_EMAILS`
- Marcador `planilhaBaseline` sincroniza na nuvem (incluído no `syncMerge`) — importação não se repete entre dispositivos

---

## v2.7

Corrigido:

- Configurações → "Horas por semana" (e demais parâmetros do scheduler) agora podem ser aplicados à semana atual: ao salvar com mudanças, o app oferece replanejar a semana corrente — antes, o plano congelado da segunda-feira ignorava a nova capacidade até a semana seguinte
- Tarefas já concluídas que permanecerem no novo plano continuam marcadas (uid estável)
- `spApply()`/`spReset()` agora sincronizam na nuvem (`queueCloudSave`) — mudanças de parâmetros não ficavam apenas no localStorage
- `fullRebuildScheduler()` re-renderiza o board da semana (antes só atualizava métricas de uma semana arbitrária)

---

## v2.8

Corrigido:

- Configurações: campo "Conteúdo Novo (min)" agora é editável — removido `oninput="spFillForm()"` que reescrevia o campo a cada tecla
- "Horas por semana" agora bate com o calculado em "Esta semana": cada tarefa recebe seu tempo real (`min`) no momento da geração; o congelamento e a soma da semana usam esse valor em vez de fallbacks que inflavam o par NEW+Q1 (80→110 min)
- Testes de capacidade do scheduler passam a usar os valores configurados (`sp.minRev/minQ2/minQ3/minNew+minQ1`) em vez das constantes fixas `MIN_REV=25/MIN_BLOCK=35/MIN_NEW=90` — "cabe?" e "gasta" agora são idênticos
- `_minForType()` unifica o fallback de minutos por tipo de tarefa (dados legados)

Alterado:

- Revisão espaçada: o % de acerto de domínio passa a ser sempre a soma de Q1+Q2+Q3 (não é mais sobrescrito pela última revisão). O intervalo da 1ª revisão usa esse acumulado; cada revisão seguinte recalcula o intervalo pelo acerto SOMENTE da última revisão (variável `revPct` separada de `pct`)

---

## v2.9

Alterado:

- "Horas por semana" agora é META, não teto: o scheduler preenche a semana até atingir as horas configuradas. Removidos os limites fixos de 4 conteúdos novos e 3 blocos por semana — o nº de conteúdos novos agora deriva da capacidade restante, com distribuição proporcional ao peso das disciplinas

Adicionado:

- Botão "➕ Adicionar mais" em "Esta semana": anexa ao plano a próxima tarefa de maior prioridade ainda não incluída (revisão atrasada > bloco devido > revisão da semana > conteúdo novo + Q1), permitindo ultrapassar a meta
- Chip da semana mostra "Xh / meta Yh"; o botão destaca quando o plano está abaixo da meta ("N h abaixo da meta")

---

## v3.0 — Otimização de estudos

Adicionado (nova seção "🧠 Otimização de estudos" em Configurações):

- **Limite de conteúdo novo por semana (%)**: no máximo `maxNewPct`% da capacidade da semana em conteúdo novo (padrão 60%); o restante fica para revisão/blocos — protege a retenção mesmo com horas altas
- **Reta final sem conteúdo novo**: nas últimas `rampDownWeeks` semanas antes da prova (padrão 4), o scheduler corta conteúdo novo e prioriza revisão/blocos (requer data da prova configurada)
- **Interleaving de disciplinas**: as tarefas da semana são intercaladas por disciplina (round-robin) em vez de agrupadas — reduz blocagem e melhora retenção
- **Fator de facilidade (SM-2)**: o intervalo de revisão, cuja base é o acerto da última revisão, é modulado pela consistência do histórico — quem acerta consistentemente espaça mais; quem oscila/erra encurta (multiplicador 0,7–1,6)

Todos os quatro são configuráveis e sincronizam na nuvem via `STATE.schedulerParams`.

---

## v3.1

Adicionado:

- **Botão "↻ Recalcular esta semana"** em "Esta semana": arquiva o plano atual (com o progresso feito) no Histórico Semanal e recongela a semana do zero com o estado atual. Tooltip explicativo + confirmação (ação irreversível)
- **Pop-up de registro rápido**: clicar numa tarefa da semana abre um modal enxuto para inserir questões/acertos, cria a sessão (tipo derivado da tarefa) e marca a tarefa como concluída — sem precisar do painel à direita
- **Resetar conta** (Configurações → Zona de perigo, em vermelho): apaga todo o progresso e recomeça do zero, mantendo login e perfil. Dupla confirmação + backup de segurança automático (`pre-reset`) antes de apagar

---

## v3.2

Corrigido:

- **"Adicionar mais" agora respeita o limite de % de conteúdo novo** — era a causa do limite parecer ignorado após recalcular a semana: cada clique adicionava NEW+Q1 sem checar o budget. Agora antecipa revisões primeiro e, se só restar conteúdo novo além do limite, avisa e não adiciona
- **Revisões atrasadas na ordem certa**: o scheduler consumia o pool de trás para frente, agendando as de MENOR prioridade primeiro — corrigido (maior prioridade primeiro)
- **`delSess` desfaz check do registro rápido**: entradas `source:'quick'` agora são limpas ao excluir a sessão (antes só `auto_match`)

Alterado:

- **Check somente mediante registro**: as caixas de seleção em "Esta semana" e no Cronograma viram indicadores (desabilitadas) — a tarefa é riscada apenas ao registrar a atividade, pelo pop-up (clique na tarefa) ou pelo painel lateral (auto-match)
- **Seção 5 do scheduler — antecipação de revisões**: quando o budget de conteúdo novo é atingido e sobra capacidade até a meta, o scheduler puxa revisões que venceriam nas próximas semanas (mais próximas do vencimento primeiro) — a semana atinge a meta sem violar o % de novo
- Registro rápido em tarefa NEW com questões informadas grava como `Q1_DONE` (questões em `CONTENT_SEEN` não contavam no % de domínio)
- `semRecalcular` roda o auto-match imediatamente após recongelar — sessões já feitas na semana marcam as tarefas equivalentes na hora

---

## v3.3

Corrigido:

- **Conteúdo novo garantido por semana (piso)**: era a causa de "Recalcular esta semana" não trazer conteúdos novos. O parâmetro de otimização era só um TETO (`maxNewPct`), e as revisões/blocos (processados antes) consumiam todo o CAP — sobrava 0 para conteúdo novo em semanas carregadas
- Novo parâmetro **"Mín. conteúdos novos + Q1 / semana"** (`minNewCount`, padrão 2): a capacidade desse mínimo é RESERVADA antes das revisões/blocos, garantindo que toda semana avance em matéria nova
- Scheduler reestruturado: reserva de piso → revisões/blocos limitados a `CAP − reservado` → conteúdo novo (piso garantido + até o teto `maxNewPct`) → preenche o restante até a meta drenando revisões (atrasadas → semana → antecipadas)
- Simulação (semana com 40 revisões atrasadas): antes 0 novos; agora 2 novos garantidos + semana cheia até a meta

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
