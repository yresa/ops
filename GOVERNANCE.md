# 🛡️ Governança Operacional & Princípios Sagrados (`GOVERNANCE.md`)

> Os quatro pilares inegociáveis que regem a engenharia de operações da Yresá.

---

## 1. O Princípio da Idempotência Absoluta
Todo webhook, fila ou script de automação deve ser **estritamente idempotente**.
- Se o Asaas reenviar o mesmo evento de pagamento 5 vezes por oscilação de rede, a assinatura deve ser ativada exatamente uma vez e apenas uma NFS-e deve ser emitida.
- Cada evento processado armazena o seu `event_id` único no banco de dados com chave primária única (`UNIQUE`), descartando retransmissões silenciosamente.

---

## 2. A Lei do Silêncio nos Alertas
Recusamos a cultura de "fadiga de alertas" (*alert fatigue*) de empresas corporativas onde notificações apitam o dia inteiro:
- **Alertas Verdes não existem:** Não enviamos notificações para dizer "o sistema está funcionando normalmente". O silêncio é a prova de funcionamento.
- **Alertas Críticos são Cirúrgicos:** Um alerta só pode disparar notificação push no celular do fundador se houver:
  1. Falha repetida no processamento de pagamentos ou webhooks;
  2. Esgotamento súbito de franquia corporativa de API (Google Cloud);
  3. Falha massiva de SSL ou indisponibilidade total do Pórtico Web.

---

## 3. O Princípio do Pânico Zero (Runbooks Explícitos)
Nenhum ser humano deve tomar decisões sob estresse de cabeça fria sem apoio:
- Para cada falha concebível, existe um arquivo em [`playbooks-and-runbooks/`](playbooks-and-runbooks/) com os comandos exatos de terminal prontos para copiar e colar.
- Reverter uma versão de produção (*rollback*) deve requerer no máximo **dois comandos e menos de 90 segundos**.

---

## 4. O Desacoplamento da Vida do Fundador
A infraestrutura é desenhada para operar de forma autônoma:
- O sono, as viagens e os momentos offline do fundador são sagrados.
- Falhas transitórias devem possuir retentativas exponenciais com jitter (*exponential backoff*), resolvendo a maioria dos engasgos de rede sem intervenção humana.
