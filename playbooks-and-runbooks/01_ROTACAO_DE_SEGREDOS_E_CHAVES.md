# 🔑 Runbook: Rotação Emergencial de Chaves & Segredos (`01_ROTACAO_DE_SEGREDOS_E_CHAVES.md`)

> **Cenário:** Uma chave de API (Google Cloud, Asaas, Turnstile, Resend ou token de banco) foi acidentalmente exposta ou necessita de rotação periódica.

---

## ⏱️ Meta: Executar em menos de 5 minutos, com zero downtime.

---

## 1. Rotação de Segredos do Cloudflare Worker (`yresa-web`)

Se `RESEND_API_KEY` ou `TURNSTILE_SECRET_KEY` precisarem de troca:

1. Gere a nova credencial no respectivo painel (Resend ou Cloudflare Turnstile).
2. No terminal do seu notebook, execute:
   ```bash
   cd ~/Documents/antigravity/yresa-web
   npx wrangler secret put RESEND_API_KEY
   # cole a nova chave quando solicitado

   npx wrangler secret put TURNSTILE_SECRET_KEY
   # cole a nova chave quando solicitado
   ```
3. A Cloudflare atualiza as instâncias de borda no mundo todo em menos de 15 segundos sem reiniciar o serviço.
4. Revogue a chave antiga no painel do provedor.

---

## 2. Rotação da Chave de IA do Google Cloud (Gemini / Vertex AI)

Se a chave de produção do Gateway Mínimo for comprometida:

1. No Google Cloud Console → **APIs & Services** → **Credentials**:
   - Crie uma nova API Key ou gere uma nova Service Account Key;
   - Restrinha a chave exclusivamente aos serviços: `Vertex AI API` e `Generative Language API`.
2. No painel de hospedagem do Gateway (ex.: Cloud Run):
   ```bash
   gcloud run services update yresa-gateway \
     --update-env-vars GEMINI_API_KEY="AIzaSy..." \
     --region southamerica-east1
   ```
3. Confirme o tráfego saudável no endpoint de healthcheck:
   ```bash
   curl -I https://api.yresa.app/health
   ```
4. Exclua a chave antiga no Google Cloud Console.

---

## 3. Rotação do Token de Webhook do Asaas

1. Acesse o painel do Asaas → **Configurações da Conta** → **Integrações** → **Webhooks**.
2. Gere um novo **Token de Autenticação de Webhook**.
3. Atualize o Gateway Mínimo com a nova variável `ASAAS_WEBHOOK_TOKEN`.
4. Salve no painel do Asaas. O Asaas testará o envio de um ping e confirmará o status 200 OK.
