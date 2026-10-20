# 🌩️ Runbook: Contingência de Modelos de Inteligência Artificial (`03_CONTINGENCIA_DE_PROVEDORES_DE_IA.md`)

> **Cenário:** O Google Cloud apresenta degradação temporária ou latência anormal em um modelo específico da família Gemini.

---

## 1. Roteamento Inteligente no Gateway Mínimo

A arquitetura do Gateway deve prever **fallback em cascata** entre variantes da mesma família Google Cloud sob o mesmo contrato corporativo de retenção zero:

```text
[ Requisição do Usuário ]
          │
          ▼
   Tenta: Gemini 3.5 Flash-Lite (Primário, ultrarrápido)
          │
          ├── (Sucesso: retorna streaming)
          │
          └── (Falha 503 / Timeout > 8s)
                     │
                     ▼ Fallback Gracioso
              Tenta: Gemini 3.6 Flash (Secundário)
                     │
                     └── (Se ambos falharem: erro humanizado, não técnico)
```

---

## 2. A Mensagem Humanizada ao Usuário (Recusa ao "Dev-Speak")

Se a nuvem estiver indisponível, o Gateway e o App nunca devem exibir mensagens como `HTTP 500 Internal Server Error`, `JSON RPC Error` ou `Gemini quota exceeded`.

A mensagem oficial da persona Yresá deve ser:
> *"O silêncio agora é do lado de cá. Nosso motor de reflexão está respirando um pouco mais devagar para voltar ao ritmo. Seu relato já está guardado no seu dispositivo, e logo poderemos conversar sobre ele."*
