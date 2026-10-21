# 🛠️ Yresá Ops — Território Soberano de Engenharia de Operações, Pipes & Sentinelas

> *"A paz de espírito do fundador é o alicerce invisível que sustenta a serenidade do santuário."*

---

## 🧭 1. O Papel do `yresa-ops` na Organização

O **`yresa-ops`** é o território soberano que abriga a infraestrutura como código, os canos de conexão (*pipes*), os receptores de webhooks, os planos de contingência (*runbooks*) e o sistema de sentinelas do ecossistema **Yresá**.

Ele resolve a tensão essencial entre:
1. **A Borda Viva (24/7 Serverless & Nuvem):** Processos que precisam escutar o mundo dia e noite sem interrupção (webhooks do Asaas, renovações da Apple/Google, suporte e certificados SSL), com custo zero quando ociosos;
2. **A Mesa de Comando (O Notebook do Fundador com Antigravity):** O ambiente reflexivo, seguro e soberano onde o fundador e os agentes trabalham em alto nível, sem a obrigação de manter a máquina ligada para a empresa sobreviver.

---

## 📁 2. Estrutura do Território

```text
yresa-ops/
├── README.md                                  # Este manifesto e mapa de operações
├── GOVERNANCE.md                              # Filosofia de Pânico Zero, Idempotência e Silêncio
├── CHANGELOG.md                               # Histórico de alterações e releases de infraestrutura
│
├── architecture/                              # Arquitetura dos Sistemas
│   ├── 01_DUPLA_CAMADA_BORDA_VS_MESA.md       # Borda Serverless 24/7 vs. Mesa Antigravity
│   └── 02_MAPA_DE_PIPES_E_WEBHOOKS.md         # Diagrama visual de todos os canos do ecossistema
│
├── playbooks-and-runbooks/                    # "Manuais de Pânico Zero"
│   ├── 01_ROTACAO_DE_SEGREDOS_E_CHAVES.md     # Procedimento se uma chave vazar (Google, Asaas, etc.)
│   ├── 02_ROLLBACK_E_INCIDENTES_CRITICOS.md   # Como reverter versões na borda ou gateway em minutos
│   └── 03_CONTINGENCIA_DE_PROVEDORES_DE_IA.md # Roteamento e contingência caso a nuvem oscile
│
├── sentinels-and-alerts/                      # Monitoramento sem ansiedade (Sentinelas)
│   ├── 01_FILOSOFIA_DE_ALERTAS_SILENCIOSOS.md # Monitoramento discreto: alertas apenas em falha real
│   └── 02_SPEC_SENTINELA_TELEGRAM.md          # Especificação do bot privado de alertas do fundador
│
├── edge-and-pipes/                            # Automações de Borda Serverless (24/7)
│   ├── 01_ESPECIFICACAO_WEBHOOKS_ASAAS.md     # Contrato de webhooks, idempotência e NFS-e
│   └── 02_CRONS_E_ROTINAS_NOTURNAS.md         # Crons da Cloudflare / GitHub Actions noturnos
│
└── antigravity-cockpit/                       # Automações da Mesa de Operação do Fundador
    ├── 01_BOLETIM_MATINAL_SPEC.md             # Especificação do briefing matinal ao abrir o laptop
    ├── 02_CHECKLIST_PRE_PRODUCAO.md           # Roteiro de checagem obrigatória antes do lançamento
    └── 03_COCKPIT_MOVEL_TELEGRAM_BOT_SPEC.md  # Especificação do bot conversacional executivo no Telegram
```

---

## 🔗 3. Como o `yresa-ops` se Conecta aos Demais Territórios

| Território | O que o `yresa-ops` gerencia para ele |
| :--- | :--- |
| **`yresa-web`** | Deploys automáticos na Cloudflare via GitHub Actions, monitoramento de SSL, CSP e taxa de entrega do Resend. |
| **`cronia` (App & Gateway)** | Orquestração do Gateway no Cloud Run, migrações de schema no Postgres e integridade dos endpoints de IA. |
| **`yresa-legal`** | Garantia técnica de que os prazos de purga e a exclusão de contas (`DELETE /api/v1/account`) aconteçam de fato. |
| **`yresa-finance`** | Alimentação automática dos relatórios com os dados numéricos de webhooks do Asaas e repasses das lojas. |
