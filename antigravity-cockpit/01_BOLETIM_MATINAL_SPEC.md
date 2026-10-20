# ☕ O Boletim Matinal da Yresá (`01_BOLETIM_MATINAL_SPEC.md`)

> **Conceito:** O ritual silencioso da manhã. Quando Lucas abre o computador e inicia o Antigravity, o sistema não dispara notificações ansiosas: ele entrega uma carta serena de 1 minuto sobre a saúde da instituição.

---

## 1. O Formato do Boletim

```text
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
☀️ Yresá · Boletim da Manhã (20 de Outubro de 2026)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🏛️ Santuário & Borda:
  - Pórtico Web: Saudável (100% uptime nas últimas 24h)
  - Formulário de Suporte: 0 mensagens pendentes
  - Latência Média do Gateway: 180ms

💳 Movimento Econômico (Últimas 24h):
  - Novas Assinaturas: 2 (1 Anual Asaas, 1 Mensal Apple IAP)
  - Assinantes Ativos: 48
  - Custo de IA do dia: R$ 8,14 (Google Cloud Vertex AI)

⚖️ Governança & Conformidade:
  - Pedidos de Exclusão de Conta: 0
  - Integridade da Matriz de Rastreabilidade: 100%

Desejamos um dia de escuta serena e bom trabalho no ateliê.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 2. Implementação com Antigravity Slash Command ou Script Soberano

Este boletim é gerado por uma chamada simples do Antigravity contra o endpoint consolidado do Gateway:
```bash
curl -s -H "Authorization: Bearer $YRESA_ADMIN_KEY" https://api.yresa.app/v1/ops/morning-briefing
```
O Antigravity recebe o JSON puro e formata em prosa poética e acolhedora, pronta para leitura em poucos segundos.
