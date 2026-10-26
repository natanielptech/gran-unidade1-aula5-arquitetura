# 06 — Rastreabilidade

```mermaid
flowchart LR
    ADR["ADR-04<br/>por quê"] --> SEQ["Diagrama de sequência<br/>ordem e tipo das mensagens"]
    ADR --> EST["Máquina de estados<br/>impacto no ciclo de vida"]
    ADR --> C4["C4 nível 2<br/>quais partes existem"]
```

| Artefato | Link |
|---|---|
| ADR-04 | [adr/ADR-04-desacoplamento-assincrono.md](../adr/ADR-04-desacoplamento-assincrono.md) |
| Sequência | [diagramas/depois/sequencia-checkout.mmd](../diagramas/depois/sequencia-checkout.mmd) |
| Estados | [diagramas/depois/pedido-estados.mmd](../diagramas/depois/pedido-estados.mmd) |
| C4 | [diagramas/depois/c4-conteineres.mmd](../diagramas/depois/c4-conteineres.mmd) |

Os três artefatos contam a mesma história em níveis diferentes; o ADR explica o porquê. Versionados juntos com o código, mantêm a documentação coerente.
