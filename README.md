# Aula 5 — Modelagem e Registro de Decisões Arquiteturais com Apoio de IA

> *Aula 5 — Prof. Nataniel Paiva · Curso: Arquitetura de Sistemas e Microsserviços · Unidade 1*

Estudo de caso que integra **UML, C4 e ADR** contando a história completa de uma decisão arquitetural: **problema → alternativas (com apoio de IA) → avaliação crítica → [ADR-04](adr/ADR-04-desacoplamento-assincrono.md) → diagramas atualizados**.

## Fluxo de decisão

```mermaid
flowchart LR
    E1["1. Formular problema<br/>e atributos de qualidade"] --> E2["2. Explorar alternativas<br/>com IA (prós, contras, riscos)"]
    E2 --> E3{"3. Avaliar criticamente<br/>(verificação HUMANA)"}
    E3 -- "reprovado / dúvida" --> E2
    E3 -- aprovado --> E4["4. Decidir e justificar"]
    E4 --> E5["5. Registrar ADR<br/>e atualizar UML + C4"]
    classDef ia fill:#e8f0fe,stroke:#4a6fd6
    classDef humano fill:#e6f4ea,stroke:#2e7d32
    class E2 ia
    class E1,E3,E4,E5 humano
```

Azul = apoio da IA · Verde = passos humanos. Detalhes em [docs/03](docs/03-fluxo-em-5-etapas.md).

## O problema

Na Black Friday o checkout do monolito fica lento/indisponível: pagamento, reserva de estoque e notificações rodam de forma síncrona e sequencial. Ver [docs/01](docs/01-cenario-e-problema.md).

## Alternativas

```mermaid
flowchart TD
    P["Problema: checkout monolítico e síncrono<br/>não suporta picos"]
    P --> A1["(i) Escalar verticalmente<br/>+ barato · não resolve acoplamento"]
    P --> A2["(ii) Serviço de pagamento síncrono"]
    P --> A3["(iii) Pagamento dedicado + eventos assíncronos<br/>+ desempenho · consistência eventual"]
    A3 ==> D["Decisão: ADR-04 (aceito)"]
```

## Antes × depois: sequência

**Antes** (síncrono e sequencial):

```mermaid
sequenceDiagram
    actor Cliente
    participant Web as Aplicação Web
    participant Pedido as PedidoService
    participant Pag as Pagamento (módulo)
    participant Est as Estoque (módulo)
    participant Not as Notificação (módulo)
    Cliente->>Web: finalizarCompra()
    Web->>Pedido: criarPedido()
    Pedido->>Pag: autorizarPagamento()
    Pag-->>Pedido: aprovado
    Pedido->>Est: reservarItens()
    Est-->>Pedido: reservado
    Pedido->>Not: enviarNotificacao()
    Not-->>Pedido: enviada
    Pedido-->>Web: pedidoConfirmado
    Web-->>Cliente: confirmação
```

**Depois** (autorização síncrona; estoque e notificações assíncronos — `->>` síncrono, `-)` assíncrono):

```mermaid
sequenceDiagram
    actor Cliente
    participant Web as Aplicação Web
    participant Pedido as PedidoService
    participant Pag as PagamentoService
    participant Bus as BarramentoEventos
    participant Est as EstoqueService
    participant Not as NotificacaoService
    Cliente->>Web: finalizarCompra()
    Web->>Pedido: criarPedido()
    Pedido->>Pag: autorizarPagamento()
    Pag-->>Pedido: pagamentoAprovado
    Pedido-)Bus: publica PedidoPago
    Pedido-->>Web: pedidoConfirmado
    Web-->>Cliente: confirmação
    Note over Pedido,Cliente: tempo percebido = só a autorização do pagamento
    Bus-)Est: PedidoPago
    Bus-)Not: PedidoPago
    Est-)Bus: EstoqueIndisponivel (falha rara)
    Note over Pag,Est: ação compensatória: estornar pagamento e cancelar pedido
```

Estados do Pedido e C4 nível 2 (antes/depois) em [docs/05](docs/05-atualizacao-dos-diagramas.md).

## Custo aceito

Consistência eventual (estoque/notificações) + ação compensatória (`EstoqueIndisponivel` → estorno e cancelamento) + observabilidade adicional.

## Mapa do repositório

| Caminho | Conteúdo |
|---|---|
| [docs/](docs/) | Cenário, IA, fluxo, alternativas, diagramas, rastreabilidade |
| [adr/](adr/ADR-04-desacoplamento-assincrono.md) | ADR-04 |
| [diagramas/](diagramas/) | Fontes `.mmd` antes/depois |
| [prompts/](prompts/) | Prompts de exemplo com verificação humana |
| [checklist-verificacao-humana.md](checklist-verificacao-humana.md) | Checklist |
| [docs/briefing.md](docs/briefing.md) | Briefing original |

> Tecnologias de broker/banco não foram especificadas nos slides; usamos rótulos genéricos (*exemplo adicional*).
