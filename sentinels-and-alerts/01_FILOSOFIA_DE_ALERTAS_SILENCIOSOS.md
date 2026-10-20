# 🤫 Filosofia das Sentinelas Silenciosas (`01_FILOSOFIA_DE_ALERTAS_SILENCIOSOS.md`)

> **Princípio:** Monitoramento sem ansiedade. A tecnologia deve trabalhar em silêncio e só interromper o fundador diante de um fato real que requeira ação humana.

---

## 1. O Problema das Ferramentas Corporativas Tradicionais

Ferramentas como Datadog, New Relic ou PagerDuty foram desenhadas para empresas com centenas de operadores em regime de escala. Elas geram:
- Dezenas de e-mails diários de "aviso informativo";
- Ansiedade crônica ("será que o gráfico subiu?");
- Custos exorbitantes de infraestrutura que violam a Entropia Mínima Justa.

---

## 2. A Heurística das Sentinelas Yresá

As sentinelas da Yresá são **pequenos scripts serverless e crons de baixo consumo** com uma única missão:
1. **Verificação de Pulso Silenciosa:** A cada 15 minutos, um Worker gratuito na Cloudflare testa se:
   - `yresa.example/` responde HTTP 200;
   - `api.yresa.app/health` responde HTTP 200;
   - O certificado SSL tem mais de 15 dias de validade;
2. **A Regra de Ouro:** Se estiver tudo bem, **nada acontece**. Nenhuma mensagem, nenhum ping, nenhum log ruidoso.
3. **Disparo de Emergência:** Se o teste falhar **3 vezes consecutivas** (evitando falsos positivos de oscilação momentânea de rede), a sentinela envia uma única notificação push no Telegram privado do fundador com o link direto do runbook de resolução.
