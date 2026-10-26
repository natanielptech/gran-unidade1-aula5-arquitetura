# 03 — Fluxo em 5 etapas

| # | Etapa | Quem |
|---|---|---|
| 1 | Formular o problema e os atributos de qualidade | Humano |
| 2 | Explorar alternativas pedindo prós, contras e riscos | **IA** (com humano) |
| 3 | **Avaliar criticamente**: fatos técnicos, custos, riscos, vieses (documentação oficial, testes de carga, especialistas) | Humano — **nunca opcional** |
| 4 | Decidir e justificar | Humano |
| 5 | Registrar em ADR e atualizar UML + C4 | Humano (IA pode revisar) |

```mermaid
flowchart LR
    E1["1. Formular problema"] --> E2["2. Explorar com IA"]
    E2 --> E3{"3. Avaliação crítica HUMANA"}
    E3 -- "reprovado / dúvida" --> E2
    E3 -- aprovado --> E4["4. Decidir"] --> E5["5. ADR + UML + C4"]
    classDef ia fill:#e8f0fe,stroke:#4a6fd6
    classDef humano fill:#e6f4ea,stroke:#2e7d32
    class E2 ia
    class E1,E3,E4,E5 humano
```

> Sem a etapa 3, a IA deixa de ser "apoio à decisão" e vira delegação irresponsável.
