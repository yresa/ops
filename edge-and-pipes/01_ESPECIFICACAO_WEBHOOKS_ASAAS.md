# 💳 Especificação: Webhook do Asaas & Idempotência (`01_ESPECIFICACAO_WEBHOOKS_ASAAS.md`)

> **Objetivo:** Definir o contrato do receptor de eventos do Asaas no Gateway para garantir ativação imediata de assinaturas e emissão correta de NFS-e.

---

## 1. Eventos Críticos do Asaas

O Gateway escuta estritamente 4 eventos do Asaas:

1. **`PAYMENT_RECEIVED` (Pagamento Confirmado):**
   - Transição do plano para `active`;
   - Reseta/reabastece a franquia mensal de tokens e voz no `token_ledger`;
   - Dispara solicitação de emissão automatizada de NFS-e perante a prefeitura.
2. **`PAYMENT_OVERDUE` (Pagamento em Atraso):**
   - Ativa período de carência (*grace period*) de 3 dias sem interromper o acesso do usuário;
   - App exibe aviso discreto para atualização da forma de pagamento.
3. **`SUBSCRIPTION_CANCELED` (Cancelamento de Assinatura):**
   - Atualiza `subscriptions.status = 'canceled'`;
   - Mantém o plano ativo até o término do ciclo de 30 dias já pago;
   - **Garante incondicionalmente:** o acervo local no SQLite continua 100% acessível para leitura e exportação.
4. **`PAYMENT_REFUNDED` (Reembolso / Arrependimento CDC Art. 49):**
   - Cancela imediatamente a assinatura administrativa;
   - Registra o estorno na conciliação financeira do `yresa-finance`.

---

## 2. Contrato de Idempotência em Banco

Para evitar duplicação em caso de retransmissão de rede pelo Asaas:

```sql
CREATE TABLE processed_webhook_events (
    id VARCHAR(128) PRIMARY KEY, -- ID único do evento enviado pelo Asaas (ex.: evt_12345)
    provider VARCHAR(32) NOT NULL DEFAULT 'asaas',
    event_type VARCHAR(64) NOT NULL,
    processed_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

Se o `id` já existir na tabela, o endpoint retorna HTTP 200 OK imediatamente sem reprocessar nada.
