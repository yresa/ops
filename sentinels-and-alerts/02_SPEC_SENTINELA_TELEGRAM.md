# 🤖 Especificação: Sentinela Telegram Privada (`02_SPEC_SENTINELA_TELEGRAM.md`)

> **Objetivo:** Especificar o micro-worker de baixo custo que envia alertas diretamente ao chat privado do fundador.

---

## 1. Arquitetura da Sentinela

```text
[ Cloudflare Cron Trigger (a cada 15 min) ]
                  │
                  ▼
         [ Worker: sentinela.ts ]
                  │
         ┌────────┴────────┐
         ▼                 ▼
   Check Pórtico     Check Gateway
   (HTTP 200?)       (Health 200?)
         │                 │
         └────────┬────────┘
                  │ (Se 3 falhas consecutivas)
                  ▼
        [ Telegram Bot API ]
                  │
                  ▼
     [ Chat Privado do Lucas ]
     "⚠️ Sentinela Yresá: O Gateway não respondeu após 3 tentativas.
      Runbook: playbooks/02_ROLLBACK_E_INCIDENTES_CRITICOS.md"
```

---

## 2. Payload do Bot (Simplicidade Máxima)

A sentinela utiliza a API oficial de bots do Telegram (`POST https://api.telegram.org/bot<TOKEN>/sendMessage`), sem dependências pesadas de terceiros:

```typescript
interface SentinelAlert {
  service: 'portico' | 'gateway' | 'asaas_webhook';
  status: number;
  message: string;
  runbookUrl: string;
}

export async function sendTelegramAlert(alert: SentinelAlert, botToken: string, chatId: string) {
  const text = [
    `🚨 *Sentinela Yresá · Alerta Operacional*`,
    ``,
    `*Serviço:* \`${alert.service}\``,
    `*Status:* \`${alert.status}\``,
    `*Detalhes:* ${alert.message}`,
    ``,
    `📖 *Runbook de Resolução:*`,
    alert.runbookUrl,
  ].join('\n');

  await fetch(`https://api.telegram.org/bot${botToken}/sendMessage`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ chat_id: chatId, text, parse_mode: 'Markdown' }),
  });
}
```
