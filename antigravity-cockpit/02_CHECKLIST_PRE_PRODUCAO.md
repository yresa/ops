# 🚀 Checklist Pré-Produção & Entrada em Operação (`02_CHECKLIST_PRE_PRODUCAO.md`)

> **Objetivo:** O roteiro de verificação final antes de abrir as portas do santuário ao público e submeter os aplicativos às lojas.

---

## 🏛️ 1. Pórtico Web (`yresa-web`)
- [ ] Domínio oficial configurado no Cloudflare DNS com proxy ativo (nuvem laranja);
- [ ] Chave de produção do Cloudflare Turnstile criada e aplicada (`PUBLIC_TURNSTILE_SITE_KEY` e `TURNSTILE_SECRET_KEY`);
- [ ] Domínio de envio de e-mail verificado no Resend (SPF, DKIM e DMARC com status verde no DNS);
- [ ] Chave de produção do Resend aplicada no Cloudflare Worker (`RESEND_API_KEY`);
- [ ] Variável `PUBLIC_CF_ANALYTICS_TOKEN` configurada para Web Analytics sem cookies;
- [ ] Trava de publicação ativada (`LEGAL_REQUIRE_APPROVED=true`).

---

## 📱 2. Aplicativo Móvel (`yresa-app` / Cronia)
- [ ] Fontes `Newsreader` e `Plus Jakarta Sans` empacotadas em `assets/fonts/` e `GoogleFonts.config.allowRuntimeFetching = false;`;
- [ ] Botão `Excluir minha conta` implementado em `Ajustes → Conta` chamando `DELETE /api/v1/account` (Apple Guideline 5.1.1(v));
- [ ] *Sign in with Apple* implementado e visível com igual destaque ao Google Sign-In no iOS (Apple Guideline 4.8);
- [ ] Assinaturas In-App (Apple StoreKit e Google Play Billing) testadas em ambiente Sandbox com botão *Restaurar compras*;
- [ ] URLs públicas vinculadas no App Store Connect e Google Play Console:
  - Política de Privacidade: `https://[dominio]/privacidade`
  - Termos de Uso / EULA: `https://[dominio]/termos`
  - Suporte: `https://[dominio]/suporte`

---

## ⚖️ 3. Governança Jurídica (`yresa-legal`)
- [ ] Parecer e chancela formal do advogado registrados em `approvals/APPROVAL_LOG.md`;
- [ ] Todos os marcadores `[[A DEFINIR]]` eliminados em `privacidade.md` e `termos.md`;
- [ ] Rodar `npm run sync:legal` no `yresa-web` para puxar os textos com `status: aprovado`.

---

## 🪙 4. Governança Financeira (`yresa-finance`)
- [ ] Conta corporativa aberta no Asaas em nome da SLU e chaves de produção de webhook cadastradas;
- [ ] Empresa inscrita no *Apple App Store Small Business Program* (taxa de 15%);
- [ ] Taxa reduzida de 15% habilitada no Google Play Console;
- [ ] Projeto de produção no Google Cloud com faturamento ativado (*Vertex AI Paid Tier*) e limites de cota configurados.
