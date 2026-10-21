# Yresá Ops — Backlog de Operações, Resiliência & Sentinelas

> Documento vivo para registro de ações de infraestrutura serverless, sentinelas de monitoramento, pipelines de webhook e o cockpit do fundador no ecossistema Yresá.

---

## Estrutura de Cada Item do Backlog
Cada item registrado neste documento responde a cinco perguntas fundamentais:
1. **O quê?** (Título e resumo claro da ação de engenharia)
2. **Por que fazer?** (Fundamentação operacional, proteção de resiliência ou pânico zero)
3. **Quando fazer?** (Critério ou gatilho de ativação operacional)
4. **Como fazer?** (Estratégia técnica, arquivos tocados e dependências)
5. **Critério de Conclusão** (Como sabemos que está pronto e validado)

---

## 📋 Itens Mapeados no Backlog

### [OPS-01] Cockpit Executivo Móvel no Telegram: Bot Conversacional 24/7 no Cloud Run

* **Status:** Backlog
* **Iniciativa / Épico:** Cockpit Executivo no Telegram
* **Prioridade:** Alta (Centro de Comando do Fundador)
* **Domínios / Módulos Envolvidos:** `antigravity-cockpit/03_COCKPIT_MOVEL_TELEGRAM_BOT_SPEC.md`, `yresa/cloud`
* **Referências:** Especificação do Cockpit Móvel, Telegram Bot API, Google Cloud Run.

#### 1. Contexto & Por Que Fazer
O fundador frequentemente viaja para conferências, palestras e compromissos externos sem o laptop à mão. Ter um canal seguro, criptografado e direto no Telegram para checar o status de produção, o consumo de tokens e a saúde dos repositórios elimina a ansiedade operacional e permite governança 24/7 com custo zero quando ocioso (Scale-to-Zero).

#### 2. Quando Fazer (Gatilhos de Ativação)
- Quando o aplicativo entrar na fase de testes fechados (Beta) e houver transações ativas de usuários na nuvem.
- Na preparação para a primeira viagem ou compromisso público do fundador fora da mesa de trabalho.

#### 3. Como Fazer (Estratégia Cirúrgica)
1. Criar o bot via `@BotFather` no Telegram e extrair o token seguro.
2. Implementar um micro-serviço em Go no Cloud Run (ou endpoint em `yresa/cloud`) que escuta Webhooks HTTPS do Telegram.
3. Configurar a trava estrita de identidade (`msg.from.id === LUCAS_TELEGRAM_ID`).
4. Conectar ferramentas de leitura via Function Calling com Gemini Flash:
   - Consulta a commits e status de CI na organização `yresa` via GitHub API.
   - Consulta de orçamento e consumo via Google Cloud Billing API.
   - Consulta de saldo e faturamento via API do Asaas.

#### 4. Riscos Mitigados
- Elimina a dependência de carregar o computador pessoal para checagens de emergência.
- Previne vazamento de dados com autenticação estrita no nível de remetente do Telegram.

#### 5. Critério de Conclusão
- Mensagem `/status` enviada pelo celular do Lucas respondendo com o boletim executivo em < 2 segundos.
- Mensagens de outros IDs ignoradas silenciosamente sem consumo de computação.

---

### [OPS-02] Sentinela Silenciosa de Uptime, Latência e Alertas no Telegram

* **Status:** Backlog
* **Iniciativa / Épico:** Cockpit Executivo no Telegram
* **Prioridade:** Alta (Resiliência & Pânico Zero)
* **Domínios / Módulos Envolvidos:** `sentinels-and-alerts/02_SPEC_SENTINELA_TELEGRAM.md`, Cloudflare Workers
* **Referências:** Filosofia de Alertas Silenciosos do Yresá.

#### 1. Contexto & Por Que Fazer
Notificações constantes geram fadiga de alerta. A Sentinela Silenciosa opera sob a filosofia de não incomodar o fundador por oscilações transitórias de rede, mas alertar de forma inequívoca caso o Pórtico Web ou o Gateway Cloud Run falhem persistentemente (3 tentativas consecutivas).

#### 2. Quando Fazer (Gatilhos de Ativação)
- Assim que o Gateway em `yresa/cloud` for provisionado no Cloud Run com seu domínio final.
- Concomitante ao apontamento de DNS de produção do `yresa/web`.

#### 3. Como Fazer (Estratégia Cirúrgica)
1. Implementar o worker `sentinela.ts` em Cloudflare Workers com acionamento periódico via Cron Trigger (a cada 15 minutos).
2. O worker efetua ping HTTP nos endpoints `/healthz` do Gateway e na rota principal do Pórtico.
3. Se houver falha consecutiva, dispara mensagem estruturada no chat privado do Telegram com link direto para o runbook correspondente (`playbooks-and-runbooks/02_ROLLBACK_E_INCIDENTES_CRITICOS.md`).

#### 4. Riscos Mitigados
- Descoberta de quedas de serviço antes que os usuários finais ou clientes reclamem.
- Redução drástica da ansiedade do operador: se o Telegram está em silêncio, a empresa está saudável.

#### 5. Critério de Conclusão
- Simulação de queda de endpoint de teste gerando o alerta no Telegram em até 15 minutos.
- Execução contínua sem falsos positivos em condições normais de tráfego.

---

### [OPS-03] Receptor de Webhooks do Asaas com Idempotência e Emissão de NFS-e

* **Status:** Backlog
* **Iniciativa / Épico:** Faturamento Asaas & Automação Fiscal
* **Prioridade:** Alta (Fluxo de Caixa & Conformidade Fiscal)
* **Domínios / Módulos Envolvidos:** `edge-and-pipes/01_ESPECIFICACAO_WEBHOOKS_ASAAS.md`, `yresa/finance`
* **Referências:** Contrato de Webhooks Asaas, API de Cobranças.

#### 1. Contexto & Por Que Fazer
Quando um usuário assina o Plano Membro via Pix ou Cartão no Asaas, o webhook precisa processar a confirmação de pagamento instantaneamente, liberar o acesso na conta do usuário e agendar a emissão da Nota Fiscal de Serviço (NFS-e), sem risco de processamento duplicado em caso de retentativas de rede.

#### 2. Quando Fazer (Gatilhos de Ativação)
- Na homologação das primeiras assinaturas de teste no ambiente de sandbox do Asaas.
- Antes da liberação da página de checkout público no site institucional.

#### 3. Como Fazer (Estratégia Cirúrgica)
1. Criar endpoint `/v1/webhooks/asaas` protegido por token de autenticação nos cabeçalhos (`asaas-access-token`).
2. Implementar trava de idempotência com verificação do `event_id` do Asaas antes de qualquer mutação de estado.
3. Ao receber `PAYMENT_CONFIRMED`:
   - Atualizar a assinatura do usuário para `active`;
   - Disparar ordem de emissão de NFS-e no integrador municipal;
   - Enviar notificação de boas-vindas.

#### 4. Riscos Mitigados
- Previne duplicidade de cobrança ou liberação múltipla de créditos.
- Garante conformidade fiscal automática sem intervenção manual em cada venda.

#### 5. Critério de Conclusão
- Teste de webhook com payload repetido retornando HTTP 200 sem duplicar registros no banco.
- Assinatura ativada e NFS-e gerada com sucesso em ambiente de testes.

---

### [OPS-04] Crons de Borda Serverless e Rotinas Noturnas de Manutenção

* **Status:** Backlog
* **Iniciativa / Épico:** Rotinas Serverless & Higiene Operacional
* **Prioridade:** Média (Higiene de Banco e Sanidade de Fila)
* **Domínios / Módulos Envolvidos:** `edge-and-pipes/02_CRONS_E_ROTINAS_NOTURNAS.md`
* **Referências:** Cloudflare Cron Triggers, GitHub Actions Scheduled Workflows.

#### 1. Contexto & Por Que Fazer
Tabelas de logs efêmeros, tokens expirados de sessão e tentativas antigas de webhooks acumulam entropia ao longo do tempo. Rotinas noturnas automatizadas realizam a faxina preventiva do sistema enquanto o tráfego está baixo.

#### 2. Quando Fazer (Gatilhos de Ativação)
- Quando a base de usuários começar a gerar registros volumosos de autenticação e telemetria.

#### 3. Como Fazer (Estratégia Cirúrgica)
1. Configurar rotina cron noturna executada diariamente às 03:00 (horário de Brasília).
2. Purgar tokens JWT expirados e sessões inativas há mais de 30 dias.
3. Verificar integridade dos backups remotos e consistência das tabelas do banco.

#### 4. Riscos Mitigados
- Degradação de performance em consultas por inchaço de dados temporários.
- Redução de custos de armazenamento em disco.

#### 5. Critério de Conclusão
- Execução programada registrada em logs sem falhas ou bloqueios no banco.

---

### [OPS-05] Boletim Matinal Automatizado da Yresá

* **Status:** Backlog
* **Iniciativa / Épico:** Cockpit Executivo no Telegram
* **Prioridade:** Média (Ritual de Acolhimento do Fundador)
* **Domínios / Módulos Envolvidos:** `antigravity-cockpit/01_BOLETIM_MATINAL_SPEC.md`
* **Referências:** Especificação do Boletim Matinal.

#### 1. Contexto & Por Que Fazer
Substituir a checagem manual de métricas em múltiplos painéis (Google Cloud Console, Asaas, Cloudflare e GitHub) por uma carta sintética de 1 minuto entregue ao abrir o Antigravity pela manhã.

#### 2. Quando Fazer (Gatilhos de Ativação)
- Quando o Gateway de produção estiver coletando métricas reais de tráfego e faturamento.

#### 3. Como Fazer (Estratégia Cirúrgica)
1. Consolidar os dados das últimas 24h em um endpoint `/v1/ops/morning-briefing`.
2. Formatar o resumo em prosa clara e serena (saúde da borda, novos assinantes, custo de IA do dia).
3. Disponibilizar comando `/boletim` no Cockpit Antigravity e no bot do Telegram.

#### 4. Riscos Mitigados
- Elimina a perda de tempo e a ansiedade de navegar em dezenas de dashboards pela manhã.

#### 5. Critério de Conclusão
- Leitura do boletim matinal em menos de 30 segundos com dados consolidados e confiáveis.
