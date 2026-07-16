# Gabarito: Mermaid Flowchart 📊

Template de sintaxe do Mermaid para fluxogramas simples e complexos.

---

## 1. Fluxograma Esquerda-Direita (Left-to-Right)

```text
```mermaid
flowchart LR
    A[Vite SPA Frontend] -->|Chamadas HTTPS REST| B(Hono API Server)
    B -->|Query SQL| C[(PostgreSQL Database)]
```
```

### Visualização Renderizada
```mermaid
flowchart LR
    A[Vite SPA Frontend] -->|Chamadas HTTPS REST| B(Hono API Server)
    B -->|Query SQL| C[(PostgreSQL Database)]
```

---

## 2. Fluxograma Topo-Base (Top-to-Bottom)

```text
```mermaid
flowchart TD
    Request[Entrada de Dados] --> Sanitize{Sanitizar payload?}
    Sanitize -->|Sim| Clean[Remove injeções HTML]
    Sanitize -->|Não| Block[Bloqueia requisição]
    Clean --> Save[Salva no Banco]
```
```

### Visualização Renderizada
```mermaid
flowchart TD
    Request[Entrada de Dados] --> Sanitize{Sanitizar payload?}
    Sanitize -->|Sim| Clean[Remove injeções HTML]
    Sanitize -->|Não| Block[Bloqueia requisição]
    Clean --> Save[Salva no Banco]
```
