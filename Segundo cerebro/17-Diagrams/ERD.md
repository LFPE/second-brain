# Gabarito: Mermaid Entity-Relationship Diagram (ERD) 🗄️

Template de sintaxe do Mermaid para diagramas de modelo entidade-relacionamento de bancos de dados relacionais.

---

## Exemplo de Diagrama ERD (Modelo Relacional SaaS)

```text
```mermaid
erDiagram
    TENANT ||--o{ USUARIO : "possui"
    TENANT ||--o{ CLIENTE : "gerencia"
    CLIENTE ||--o{ DOCUMENTO : "armazena"

    TENANT {
        int id PK
        string nome
        string slug
    }
    USUARIO {
        int id PK
        string nome
        string email
        int tenantId FK
    }
    CLIENTE {
        int id PK
        string nome
        int tenantId FK
    }
```
```

### Visualização Renderizada
```mermaid
erDiagram
    TENANT ||--o{ USUARIO : "possui"
    TENANT ||--o{ CLIENTE : "gerencia"
    CLIENTE ||--o{ DOCUMENTO : "armazena"

    TENANT {
        int id PK
        string nome
        string slug
    }
    USUARIO {
        int id PK
        string nome
        string email
        int tenantId FK
    }
    CLIENTE {
        int id PK
        string nome
        int tenantId FK
    }
```
