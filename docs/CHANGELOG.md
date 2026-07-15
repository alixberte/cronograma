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

## v3.4

Alterado (fórmula de prioridade — foco em incidência + consolidação):

- **Lacuna ponderada pela incidência**: `dificuldade×0,4×(inc/10)` — errar tema raro não sobe mais o score; a lacuna só pesa proporcional ao quanto o tema cai na prova
- **Consolidação no score**: −8 pontos por revisão espaçada concluída com ≥80% (teto −24) — temas sólidos descem na fila
- **Esfriamento no score**: até +20 pontos conforme o tempo sem contato se aproxima/ultrapassa o intervalo SM-2 — temas esfriando sobem
- **Proximidade da prova** agora amplifica apenas incidência + lacuna (não estágio/blocos)
- Fórmula unificada em `_priorityScore()`/`_priorityParts()` — `computeTopic` e o patch de bancas usam o mesmo código (elimina risco de divergência)

Adicionado:

- **Pop-up "Como a prioridade é calculada"**: clique no valor da prioridade (Fila inteligente ou modal do tópico) para ver o breakdown real do score daquele tema, componente a componente, com explicação de cada parcela

---

## v3.5

Corrigido (privacidade):

- **Banners das páginas de disciplina eram textos fixos no HTML com o progresso pessoal do fundador** ("GO — Fisiologia gestação 61.7%... REVISÃO VENCIDA") — apareciam para QUALQUER usuário logado, dando a impressão de troca de dados entre contas (não havia troca real: sessões/progresso sempre foram isolados por conta). Agora os banners são gerados dinamicamente pelo `renderMap()` com os dados do usuário logado: nº de assuntos, Q3 dominados, peso na prova e revisões vencidas DELE

Adicionado:

- **Dia de início da semana configurável** (Parâmetros de tempo → "Semana começa em"): qualquer dia de domingo a sábado (padrão segunda). Todo o sistema passa pelo novo helper `_weekStart()/_weekStartISO()` — scheduler, plano congelado, snapshots, sessões da semana; `_mondayISO()` virou alias. Mudar o dia oferece replanejar a semana atual

---

## v3.6

Removido:

- Configurações: cards "Preview — intervalos de revisão" e "Preview — distribuição de prioridade"
- Página "Roadmap Mai–Nov" (item da sidebar, fases estratégicas fixas e "Volume por mês" sintético)

Alterado:

- **Roadmap → Performance**: a "Cobertura projetada" agora vive dentro de Performance e é 100% derivada do cronograma real — projeção = Q3 atuais + Q3/novos efetivamente agendados nas semanas até a prova. Atualiza sozinha com qualquer mudança nas configurações (horas, %, pesos, dia da semana, data da prova). Barra dupla: sólida = atual, translúcida = projetado
- **Acerto por área**: barras verticais — grande área no eixo X, % de acerto no eixo Y (0–100%), cor própria de cada área (mesma paleta do resto do dash)
- **Heatmap horizontal** (colunas = semanas, linhas = dias, com rolagem): início = criação da conta (ou primeira atividade registrada), fim = data da prova configurada no sistema (fallback 26 semanas); chip mostra o intervalo real

Adicionado:

- **Evolução nos simulados** (Performance): gráfico de linha com % de acerto no eixo Y e prova/ano no eixo X, pontos coloridos por faixa de acerto; estado vazio amigável quando não há simulados

---

## v3.7

Adicionado:

- **Pop-up de boas-vindas no primeiro login de conta nova**: explica em tom profissional e otimista como o sistema funciona (registro de sessões → prioridade por incidência da banca + evolução própria → ciclo Q1/Q2/Q3 → revisões espaçadas → cronograma vivo) e orienta os ajustes iniciais em Configurações (data da prova, horas/semana, pesos das bancas, otimização de estudos). Botões "Começar agora" e "⚙ Ajustar configurações"
- Marcador `onboarded` no STATE (sincroniza na nuvem) — o pop-up aparece uma única vez, apenas para contas realmente novas (sem dados importados); contas existentes nunca o veem

Alterado:

- **Heatmap com janela móvel de 6 meses**: mostra sempre os últimos 6 meses até hoje, deslizando automaticamente com o passar do tempo; contas mais novas que 6 meses começam na criação/primeira atividade

---

## v3.8

Adicionado:

- **Assuntos avulsos entram na semana e no ciclo do sistema**: registrar uma sessão (painel de "Esta semana" ou modal) de um assunto que não está no plano inclui automaticamente a tarefa "📌 (extra)" no board, já marcada como concluída — contabiliza no cumprimento e nas horas da semana
- **Assuntos fora da PLANILHA** (ex.: tema pedido pela faculdade) agora entram na máquina de estados como tópicos `CUSTOM::` — o sistema dá continuidade normal: gera Q2/Q3 pendentes (+14d) e revisões espaçadas SM-2; incidência 0 (prioridade baixa por design, não compete com o conteúdo da prova)
- Toast diferenciado: "✓ Sessão registrada · incluída na semana como extra"

Corrigido:

- Clique no card de tópico da página Hoje usava `PLANILHA.find` direto — quebraria com tópicos extras; agora usa `openTopicById()` (busca no estado computado)

---

## v3.9

Adicionado:

- **Excluir semanas do Histórico Semanal, uma a uma**: cada entrada ganhou um botão 🗑 no cabeçalho. `delWeekHist(wkISO)` remove a semana com confirmação, persiste e sincroniza na nuvem. Não afeta o cronograma atual (weekHistory é arquivo de semanas encerradas); backups semanais em Minha conta continuam disponíveis para restaurar se necessário
- O botão de exclusão usa `stopPropagation` para não abrir/fechar o acordeão da semana

Nota:

- O botão "↻ Fechar semanas passadas" continua existindo — ele arquiva semanas vencidas ainda não fechadas (só age quando há o que fechar); a exclusão pedida é a nova funcionalidade acima

---

## v4.0

Corrigido:

- **Proporção de estudo por grande área com 0% agora exclui a área do conteúdo novo** (estudo focado): o bug era `discW[disc]||0.2`, que tratava peso 0 como ausente e aplicava 20% de fallback, mais um piso de 1 slot por área. Revisões/blocos de outras áreas continuam aparecendo por design — são consolidação do que já foi estudado (SM-2 não pode ser pulado sem perda)
- "Adicionar mais" também respeita a exclusão por proporção 0%
- Proporções por área agora sincronizam na nuvem (`STATE.discCfg`) e oferecem replanejar a semana atual ao salvar

Adicionado:

- **Simulados**: campos de texto livre para Banca (ex.: USP, ENARE) e Ano; o gráfico de evolução em Performance usa "banca + ano" escritos no eixo X
- **Pop-up de registro em tarefa de conteúdo novo** explica as duas formas de salvar: sem questões = só conteúdo novo (teoria); com questões e acertos = conteúdo novo + bloco Q1 automaticamente
- **Pesos das bancas com rebalanceio automático**: mover AMRIGS para 80% ajusta AMP para 20% instantaneamente (e vice-versa). Algoritmo N-ário: com mais bancas no futuro, as demais são redistribuídas proporcionalmente às suas razões atuais, fechando sempre em 100% (`BANCA_KEYS` centraliza a lista)
- Aplicar pesos de banca também oferece replanejar a semana atual

Verificado:

- Fluxo dos pesos de banca no scheduler está íntegro: sliders → `BANCA_CFG` normalizado → incidência ponderada → prioridade → ordenação dos pools; cache invalida ao mudar; persiste em `STATE.bancaCfg` (nuvem)

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
