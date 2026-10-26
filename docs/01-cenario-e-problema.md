# 01 — Cenário e problema

Sistema de e-commerce (módulo de checkout, casos de uso da Aula 2). Na **Black Friday** o checkout fica lento ou indisponível porque **pagamento, reserva de estoque e notificações** executam de forma **síncrona e sequencial** em um monolito.

Objetivo: evoluir sem comprometer a **consistência dos dados de pagamento**. Atributos priorizados: desempenho, disponibilidade e consistência (forte no pagamento).

*Exemplo adicional* — casos de uso aproximados em flowchart:

```mermaid
flowchart LR
    Cliente(["👤 Cliente"])
    Gateway(["🏦 Gateway de Pagamento"])
    subgraph Checkout["Módulo de Checkout"]
        UC1(["Finalizar compra"])
        UC2(["Efetuar pagamento"])
        UC3(["Reservar estoque"])
        UC4(["Receber notificação"])
    end
    Cliente --- UC1
    UC1 -. include .-> UC2
    UC1 -. include .-> UC3
    UC1 -. include .-> UC4
    UC2 --- Gateway
```

Decisão resultante: [ADR-04](../adr/ADR-04-desacoplamento-assincrono.md).
