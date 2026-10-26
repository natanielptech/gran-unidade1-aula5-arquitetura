# ADR-04: Desacoplamento assíncrono entre Pagamento, Estoque e Notificações

- **Status:** aceito
- **Contexto:** desempenho e disponibilidade sob pico (Black Friday); consistência eventual
  aceitável para estoque e notificações, mas não para a autorização do pagamento.
- **Decisão:** manter a autorização de pagamento síncrona; mover estoque e notificações
  para comunicação assíncrona baseada em eventos.
- **Consequências:**
  - exige observabilidade adicional (rastrear eventos entre serviços);
  - falhas como `EstoqueIndisponivel` exigem ação compensatória (estorno + cancelamento do pedido);
  - consistência eventual para estoque e notificações.
- **Alternativas consideradas:** (i) escala vertical do monolito; (ii) serviço de pagamento
  dedicado com comunicação síncrona. Ver [04-alternativas](../docs/04-alternativas.md).
- **Verificação humana:** a equipe confirmou que a mensageria sugerida pela IA é compatível
  com a nuvem já contratada. Ver [checklist](../checklist-verificacao-humana.md).
- **Diagramas atualizados:**
  [sequência](../diagramas/depois/sequencia-checkout.mmd),
  [estados do Pedido](../diagramas/depois/pedido-estados.mmd),
  [C4 nível 2](../diagramas/depois/c4-conteineres.mmd).
  Visão consolidada em [05-atualizacao-dos-diagramas](../docs/05-atualizacao-dos-diagramas.md).
