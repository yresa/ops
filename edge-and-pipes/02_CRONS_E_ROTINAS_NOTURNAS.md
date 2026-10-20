# 🌙 Crons de Nuvem & Rotinas Noturnas (`02_CRONS_E_ROTINAS_NOTURNAS.md`)

> **Objetivo:** Estabelecer as tarefas autônomas que rodam na nuvem durante a madrugada sem depender do computador do fundador estar ligado.

---

## 1. O Agendador de Borda: Cloudflare Cron Triggers

Na Cloudflare, é possível definir gatilhos de cron com sintaxe padrão de 5 campos em `wrangler.jsonc`:

```jsonc
{
  "triggers": {
    "crons": ["0 3 * * *"] // Roda todo dia às 03:00 UTC (00:00 Horário de Brasília)
  }
}
```

---

## 2. As Três Rotinas Noturnas Essenciais da Yresá

1. **Rotina 1: Purga Legal de Contas Excluídas (Art. 16 da LGPD):**
   - Executa a exclusão definitiva dos registros que completaram o prazo de carência após solicitação de exclusão pelo usuário em `/suporte` ou no app;
   - Destrói credenciais de acesso, histórico numérico de uso e registros temporários;
   - Preserva apenas as notas fiscais emitidas (NFS-e) por força do dever legal de 5 anos (CTN art. 173).
2. **Rotina 2: Fechamento Numérico do Dia (Tokenomics Diário):**
   - Consolida a soma de tokens e segundos de voz do dia em uma linha agregada da tabela `daily_usage_summary`;
   - Elimina os registros individuais detalhados de transações do dia anterior, reduzindo o volume do banco e reforçando a minimização de dados.
3. **Rotina 3: Verificação de Validade de Certificados e Chaves:**
   - Checa se há certificados SSL ou tokens de APIs que expiram nos próximos 30 dias.
