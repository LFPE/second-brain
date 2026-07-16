# Gabarito: Mermaid Sequence Diagram 🔄

Template de sintaxe do Mermaid para diagramas de sequência de troca de mensagens.

---

## Exemplo de Diagrama de Sequência (JWT Auth Flow)

```text
```mermaid
sequenceDiagram
    participant C as Cliente (SPA)
    participant S as Servidor Hono
    participant DB as Banco Postgres

    C->>S: Envia credenciais (Login POST)
    S->>DB: Consulta email na Whitelist e senha
    DB-->>S: Retorna registro do usuário
    S->>S: Valida Hash e assina Token JWT
    S-->>C: Retorna Token JWT de sessão
```
```

### Visualização Renderizada
```mermaid
sequenceDiagram
    participant C as Cliente (SPA)
    participant S as Servidor Hono
    participant DB as Banco Postgres

    C->>S: Envia credenciais (Login POST)
    S->>DB: Consulta email na Whitelist e senha
    DB-->>S: Retorna registro do usuário
    S->>S: Valida Hash e assina Token JWT
    S-->>C: Retorna Token JWT de sessão
```
