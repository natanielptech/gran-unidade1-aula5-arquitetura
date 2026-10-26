# 05 — Atualização dos diagramas (antes × depois)

Todos referenciam o [ADR-04](../adr/ADR-04-desacoplamento-assincrono.md). Fontes em [`diagramas/`](../diagramas/).

## Sequência

- Antes: [`antes/sequencia-checkout.mmd`](../diagramas/antes/sequencia-checkout.mmd) — tudo síncrono e sequencial.
- Depois: [`depois/sequencia-checkout.mmd`](../diagramas/depois/sequencia-checkout.mmd) — pagamento síncrono; estoque e notificação assíncronos; novas linhas de vida `PagamentoService` e `BarramentoEventos`. Diagrama completo no [README](../README.md).

## Máquina de estados do Pedido (continua válida)

```mermaid
stateDiagram-v2
    direction LR
    [*] --> Criado
    Criado --> AguardandoPagamento: criarPedido (síncrono)
    AguardandoPagamento --> Pago: pagamentoAprovado (síncrono)
    Criado --> Cancelado: cancelar (síncrono)
    AguardandoPagamento --> Cancelado: pagamentoRecusado / timeout / cancelar (síncrono)
    Pago --> Enviado: despachar (assíncrono)
    Enviado --> Entregue: confirmarEntrega (assíncrono)
    Pago --> Cancelado: EstoqueIndisponivel → estorno (compensação)
    Entregue --> [*]
    Cancelado --> [*]
```

A transição `Pago → Cancelado` por compensação é **consequência documentada do ADR-04**.

| Transição | Evento | Tipo |
|---|---|---|
| Criado → AguardandoPagamento | `criarPedido` | síncrono |
| AguardandoPagamento → Pago | `pagamentoAprovado` | síncrono (consistência forte) |
| Criado/AguardandoPagamento → Cancelado | `pagamentoRecusado / timeout` ou `cancelar` | síncrono |
| Pago → Enviado | `despachar` | assíncrono (evento no barramento) |
| Enviado → Entregue | `confirmarEntrega` | assíncrono (evento da transportadora) |
| Pago → Cancelado | `EstoqueIndisponivel` | compensação (estorno) |

## C4 nível 2

**Antes** (*exemplo adicional*, rótulos genéricos):

```mermaid
flowchart LR
    Cliente(["👤 Cliente"]) --> Web["Aplicação Web"]
    Web --> Mono["Monolito de E-commerce<br/>(Pedido + Pagamento + Estoque + Notificação)"]
    Mono --> BD[("Banco de Dados")]
    Mono --> GW["Gateway de Pagamento<br/>[Sistema Externo]"]
    classDef ext fill:#999,stroke:#666,color:#fff
    class GW ext
```

**Depois** (novos em amarelo): `Serviço de Pagamento` (BD próprio, único que fala com o gateway), `Barramento de Eventos`, `Serviço de Notificações`.

```mermaid
flowchart LR
    Cliente(["👤 Cliente"]) --> Web["Aplicação Web"]
    Web --> API["API de Pedidos"]
    API -- "síncrono" --> Pag["Serviço de Pagamento"]
    Pag --> PagDB[("BD de Pagamento")]
    Pag --> GW["Gateway de Pagamento<br/>[Sistema Externo]"]
    API -. "publica PedidoPago" .-> Bus{{"Barramento de Eventos"}}
    Bus -.-> Est["Serviço de Estoque"]
    Bus -.-> Not["Serviço de Notificações"]
    classDef ext fill:#999,stroke:#666,color:#fff
    classDef novo fill:#fff3cd,stroke:#b8860b
    class GW ext
    class Pag,PagDB,Bus,Not novo
```

Versão `C4Container`: [`depois/c4-conteineres.mmd`](../diagramas/depois/c4-conteineres.mmd).
