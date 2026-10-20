# 🗺️ Mapa de Pipes, Webhooks & Fluxos de Dados (`02_MAPA_DE_PIPES_E_WEBHOOKS.md`)

> **Objetivo:** Visualizar todos os canos de conexão que atravessam a instituição digital Yresá.

---

## 1. Fluxo de Assinatura & Pagamento Nacional (Asaas Pix/Cartão)

```text
[ Visitante no yresa-web / App Desktop ]
       │
       ▼ Toca em "Assinar Plano Membro"
[ Gateway Mínimo: POST /api/v1/billing/checkout ]
       │
       ▼ Cria cobrança com webhook configurado
[ Asaas Gestão Financeira ] ───> Usuário paga o Pix via QR Code no celular
       │
       ▼ Dispara Webhook: PAYMENT_RECEIVED
[ Gateway Mínimo: POST /api/v1/billing/webhook/asaas ]
       │
       ├── 1. Valida assinatura de segurança do Asaas (Token no header)
       ├── 2. Confere idempotência (event_id já processado?)
       ├── 3. Atualiza subscriptions.status = 'active'
       ├── 4. Reseta a franquia mensal em token_ledger
       └── 5. Solicita emissão da NFS-e municipal automatizada
```

---

## 2. Fluxo de Assinatura Mobile In-App (Apple App Store & Google Play)

```text
[ Usuário no iPhone / Android ]
       │
       ▼ Toca em "Assinar" com Face ID / Google Pay
[ Apple StoreKit / Google Play Billing ]
       │
       ▼ Compra aprovada
[ yresa-app ] ───> Envia receipt criptografado para o Gateway
       │
       ▼ POST /api/v1/billing/verify-receipt
[ Gateway Mínimo ] ───> Valida com a API da Apple / Google
       │
       ▼ Receipt válido: ativa assinatura local e nuvem
[ Apple / Google Server-to-Server Webhook ]
       │
       ▼ (Nas renovações automáticas mensais futuras)
[ Gateway: POST /api/v1/billing/webhook/apple-google ]
       └── Mantém a assinatura sincronizada mesmo sem o app aberto
```

---

## 3. Fluxo de Atendimento de Suporte (Zero-Storage)

```text
[ Usuário na rota /suporte ]
       │
       ▼ Preenche formulário + Cloudflare Turnstile
[ Cloudflare Worker: POST /api/support ]
       │
       ├── 1. Honeypot check (vazio?)
       ├── 2. Rate limit check (5 req/min por IP?)
       ├── 3. Validação do Turnstile no servidor Cloudflare
       └── 4. Envio direto via API da Resend
                 │
                 ▼
          [ Caixa de E-mail de Atendimento Yresá ]
          (Zero dados gravados em banco de dados ou logs no Worker)
```
