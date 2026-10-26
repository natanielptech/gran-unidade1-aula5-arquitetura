# 04 — Alternativas

| # | Alternativa | Prós | Contras / observação da IA |
|---|---|---|---|
| i | Manter monolito e escalar **verticalmente** nos picos | Menor custo | **Não resolve o acoplamento temporal** |
| ii | Serviço de pagamento dedicado, comunicação **síncrona** | Isola pagamento | Continua acoplado no tempo ao estoque/notificações |
| iii | Pagamento dedicado, **assíncrona por eventos** com estoque/notificações; **autorização continua síncrona** | Desempenho e disponibilidade sob pico | **Consistência eventual**; exige tratar falhas de entrega de eventos |

**Escolhida: (iii)**, após a equipe verificar que a mensageria sugerida pela IA é compatível com a nuvem já contratada (avaliação humana — etapa 3).

Registro: [ADR-04](../adr/ADR-04-desacoplamento-assincrono.md).
