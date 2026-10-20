# 🚨 Runbook: Reversão de Deploys & Rollback Emergencial (`02_ROLLBACK_E_INCIDENTES_CRITICOS.md`)

> **Cenário:** Um novo deploy introduziu um bug impeditivo no site público ou no Gateway e a versão anterior estável precisa ser restaurada imediatamente.

---

## 1. Rollback Instantâneo do Pórtico Web (`yresa-web`)

Na Cloudflare, todo deploy de Worker com Static Assets cria uma versão imutável (*deployment version*):

### Método A: Via Painel da Cloudflare (1 clique)
1. Acesse **Workers & Pages** → Selecione **`yresa-web`**;
2. Vá na aba **Deployments**;
3. Localize o deploy anterior que estava funcionando (com carimbo de data/hora verde);
4. Clique nos três pontinhos `...` → **Rollback to this deployment**.
5. *Resultado:* O site volta para a versão estável em ~5 segundos globalmente.

### Método B: Via CLI (Git + Wrangler)
```bash
cd ~/Documents/antigravity/yresa-web
git log -n 5 --oneline          # Localize o commit anterior estável
git checkout <COMMIT_ANTERIOR>
npm run deploy                  # Build limpo e deploy imediato da versão anterior
git checkout main               # Retorne para a main para investigar a causa raiz com calma
```

---

## 2. Rollback do Gateway Mínimo (Cloud Run)

O Cloud Run mantém todas as revisões anteriores arquivadas em prontidão:

1. Listar as últimas revisões:
   ```bash
   gcloud run revisions list --service yresa-gateway --region southamerica-east1
   ```
2. Redirecionar 100% do tráfego para a revisão anterior estável imediatamente:
   ```bash
   gcloud run services update-traffic yresa-gateway \
     --to-revisions=yresa-gateway-00042-xyz=100 \
     --region southamerica-east1
   ```
3. *Tempo de execução:* Menos de 10 segundos. O tráfego dos usuários é comutado instantaneamente.

---

## 3. O Modo de Sobrevivência do App Flutter (`yresa-app` / Cronia)

Graças à arquitetura **Local-First**, mesmo que o Gateway ou a internet do mundo inteiro caiam:
- O usuário **continua com 100% de acesso de leitura, busca local e escrita de diários**;
- O app exibe apenas o aviso discreto: *"A Yresá está sem conexão com o motor de voz agora, mas suas memórias continuam guardadas em segurança."*
- Nenhuma tela quebra nem trava por falta de backend.
