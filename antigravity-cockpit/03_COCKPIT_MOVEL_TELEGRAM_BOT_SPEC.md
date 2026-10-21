# 📱 O Cockpit Executivo Móvel no Telegram (`03_COCKPIT_MOVEL_TELEGRAM_BOT_SPEC.md`)

> **Conceito:** O centro de comando soberano do fundador no celular. Quando Lucas estiver viajando para conferências, palestras ou longe do computador, este bot conversacional seguro permite acompanhar a saúde da empresa, auditar repositórios e tomar decisões operacionais em tempo real pelo Telegram.

---

## 🏛️ 1. Filosofia & Princípios Fundamentais

1. **Soberania e Pânico Zero:** O fundador não precisa carregar o laptop para ter tranquilidade operacional.
2. **Custo Zero quando Ocioso (*Scale-to-Zero*):** Não utiliza servidores dedicados ou VMs ligadas 24h. O bot opera 100% via **Webhooks no Google Cloud Run**, acordando em milissegundos para responder e voltando a dormir.
3. **Privacidade Absoluta & Blindagem de Acesso:**
   * **Trava Rígida de Identidade (*Whitelist*):** O bot só responde se `message.from.id === LUCAS_TELEGRAM_ID`. Qualquer mensagem de terceiros é sumariamente ignorada e descartada sem processamento.
   * **Canal Criptografado:** Tráfego ponta a ponta protegido por HTTPS e credenciais no Google Secret Manager.

---

## 🧭 2. Arquitetura Técnica

```text
[ Telegram App (Celular do Lucas) ]
                │  (Mensagem de texto ou comando de voz)
                ▼  (HTTPS POST Webhook)
[ Google Cloud Run: yresa-ops-bot ] (Scale-to-Zero em Go / Python)
                │
                ├──────────────────────────────────────────────┐
                ▼                                              ▼
   [ Verificação de Identidade ]                    [ Orquestração LLM ]
   (msg.from.id == LUCAS_ID?)                        (Gemini 2.5 Flash)
                │                                              │
                ├──────────────────────────────────────────────┤
                │  (Ferramentas / Function Calling do Bot)      │
                ▼                                              ▼
    ┌──────────────────────┬──────────────────────┬──────────────────────┐
    ▼                      ▼                      ▼                      ▼
[ GitHub API (yresa/*) ] [ GCP Billing API ]    [ Asaas / Finance ]    [ Gateway Health ]
- Status dos Repos       - Custo de IA no mês    - Saldo de Caixa       - Latência Cloud Run
- Últimos Commits        - Consumo Gemini        - Novas Assinaturas    - Status dos Webhooks
- CI/CD Actions          - Alertas de Teto       - Inadimplência        - Uptime do Pórtico
```

---

## 🎛️ 3. Capacidades Conversacionais

O bot opera em dois níveis: **Comandos Rápidos Estruturados** e **Diálogo Livre com a Organização**.

### A. Comandos Rápidos
* `/status`: Boletim executivo instantâneo em 5 linhas (Uptime dos serviços, novas assinaturas no Asaas/Apple, custo de IA acumulado no mês e últimos deploys).
* `/billing`: Detalhamento do faturamento Google Cloud (quanto foi gasto com Gemini Flash, Transcribe e Cloud Run).
* `/repos`: Visão sintética dos 10 repositórios (últimos commits e saúde dos testes de CI no GitHub Actions).

### B. Consulta Livre (Conversação com Consciência Organizacional)
O fundador pode enviar mensagens naturais:
* *"Yresá, qual foi o último commit na main do app e passou no teste?"*
  ➔ O bot consulta a API do GitHub e responde com o hash, mensagem e status do CI.
* *"Como está o consumo de tokens essa semana em relação à semana passada?"*
  ➔ O bot calcula a variação e avisa se houve pico.
* *"Tem alguma issue aberta em legal ou pendência de cliente no suporte?"*
  ➔ O bot varre os canais de entrada e resume em 2 parágrafos.

---

## 🔐 4. Especificação de Segurança & Variáveis de Ambiente

O serviço roda com Service Account dedicada e consome do Google Secret Manager:

| Variável | Finalidade | Origem |
| :--- | :--- | :--- |
| `TELEGRAM_BOT_TOKEN` | Token de autenticação da Bot API | Google Secret Manager |
| `LUCAS_TELEGRAM_ID` | ID numérico exclusivo do Telegram do Lucas | Google Secret Manager / Env |
| `GITHUB_ORG_PAT` | Personal Access Token com leitura na org `yresa` | Google Secret Manager |
| `GEMINI_API_KEY` | Chave de inferência para orquestração | Google Secret Manager |
| `ASAAS_API_KEY` | Chave de leitura de faturamento do Asaas | Google Secret Manager |

---

## 📋 5. Critérios de Conclusão & Ativação Futura

- [ ] Criação do bot via `@BotFather` no Telegram e obtenção do `TELEGRAM_BOT_TOKEN`.
- [ ] Mapeamento do `LUCAS_TELEGRAM_ID` via `@userinfobot`.
- [ ] Implementação do endpoint de Webhook no Cloud Run com validação estrita de remetente.
- [ ] Integração das ferramentas de leitura (GitHub API + Cloud Billing API).
