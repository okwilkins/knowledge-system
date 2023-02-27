Zettelcasten Index: 20230227123735-d
Sequence:
Status: #idea
Zettelcasten Tags: [[Learning]], [[Diagrams]], [[slip-box/Mermaid|Mermaid]], [[Entity Relationship Diagram]], [[Programming]], [[Databases]], [[Data]]

---

```
erDiagram
    CUSTOMER ||--o{ ORDER : places
    ORDER ||--|{ LINE-ITEM : contains
    CUSTOMER }|..|{ DELIVERY-ADDRESS : uses
```

```mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : places
    ORDER ||--|{ LINE-ITEM : contains
    CUSTOMER }|..|{ DELIVERY-ADDRESS : uses
```

## References
- [[references/Mermaid|Mermaid#Entity Relationship Diagrams]]