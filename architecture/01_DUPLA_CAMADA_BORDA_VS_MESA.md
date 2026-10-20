# 🌐 A Dupla Camada: A Borda Viva 24/7 vs. A Mesa Antigravity (`01_DUPLA_CAMADA_BORDA_VS_MESA.md`)

> **Objetivo:** Estabelecer com precisão matemática a divisão de responsabilidades entre a infraestrutura que roda dia e noite na nuvem e o ambiente de trabalho local do fundador.

---

## 1. Por que essa separação é vital?

Se tentássemos rodar automações essenciais (como escutar pagamentos ou checar falhas) diretamente no notebook do fundador:
- Fechar a tampa do computador desligaria o recebimento de clientes;
- Uma queda de energia ou internet residencial travaria a operação;
- O fundador viveria em estado de vigília constante.

A solução da Yresá é o **desacoplamento em duas camadas**:

---

## 2. Camada 1: A Borda Viva (24/7 Serverless & Nuvem)

Esta camada nunca dorme, custa centavos (ou zero) quando ociosa e possui tolerância a falhas global:

| Serviço / Componente | Onde Roda | O que faz 24/7 |
| :--- | :--- | :--- |
| **Pórtico Web & Suporte** | Cloudflare Workers (`yresa-web`) | Serve o site estático instantaneamente na borda e recebe mensagens em `/api/support`. |
| **Gateway Mínimo** | Google Cloud Run / Container Serverless | Recebe chamadas de login, valida assinaturas, controla o hidrômetro e faz streaming efêmero com o Gemini. |
| **Receptor de Webhooks** | Endpoint seguro no Gateway | Ouve eventos do Asaas (Pix pago), Apple App Store (renovação/reembolso) e Google Play. |
| **Banco Administrativo** | PostgreSQL Gerenciado (ex.: Neon / Cloud SQL) | Guarda metadados de contas, contadores numéricos e logs de eventos para conciliação. |
| **Sentinelas Noturnas** | Cloudflare Cron Triggers / GitHub Actions | Roda checagens agendadas (ex.: integridade de certificados, expiração de faturas). |

---

## 3. Camada 2: A Mesa de Comando (O Notebook do Fundador com Antigravity)

Esta camada só roda quando o fundador decide sentar para trabalhar:

- **Propósito:** É a mesa de ateliê e cockpit estratégico. Onde Lucas + Antigravity escrevem novas features, analisam métricas financeiras, revisam termos legais e constroem narrativas.
- **Como ela consome a Borda:**
  - O Antigravity se comunica com o Gateway e os repositórios via chamadas seguras e CLI (`--json`);
  - Ao abrir o computador pela manhã, o Antigravity pode executar o **Boletim Matinal**, lendo os eventos das últimas 24 horas registrados na nuvem e apresentando um resumo sereno de um minuto.
