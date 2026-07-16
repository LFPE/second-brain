# Otimização de Performance e Estabilidade de Layout em React (SaaS)

## Introdução
Interfaces SaaS modernas que lidam com painéis de dados complexos, como quadros Kanban ou CRM, exigem muito do motor de renderização do navegador. Sem a devida otimização, interações comuns (como arrastar um card ou digitar na barra de pesquisa) podem causar atrasos perceptíveis (input lag), prejudicando gravemente a experiência do usuário (UX).

Neste artigo, detalho as técnicas avançadas de renderização e estabilização de layout que apliquei na interface do JurisHub para garantir respostas abaixo de 16ms (60 FPS) nas interações de CRM.

---

## ⚡ 1. Prevenção de Renderizações Desnecessárias

No React, sempre que o estado de um componente pai muda, todos os seus filhos são re-renderizados por padrão. Em uma visualização de CRM com dezenas de clientes divididos em colunas do Kanban, digitar um caractere na pesquisa principal do topo causaria o recálculo completo de todos os cards de clientes.

### Memoização com `React.memo`
Para evitar re-renderizações inúteis, encapsulei os subcomponentes mais pesados em `React.memo`. Isso instrui o React a realizar uma comparação rasa (shallow comparison) nas propriedades (props) e pular a renderização do componente se as propriedades não tiverem mudado:

```typescript
import React from 'react';

interface KanbanCardProps {
  cliente: any;
  onDragStart: (e: React.DragEvent, id: number) => void;
  onOpenDetails: (cliente: any) => void;
}

export const KanbanCard = React.memo(({ cliente, onDragStart, onOpenDetails }: KanbanCardProps) => {
  return (
    <div 
      draggable 
      onDragStart={(e) => onDragStart(e, cliente.id)}
      className="p-4 bg-zinc-900 border border-zinc-800 rounded-2xl"
    >
      <h4>{cliente.nome}</h4>
    </div>
  );
});
```

### Preservando Referências com `useCallback` e `useMemo`
A memoização falha se passarmos funções ou objetos criados em tempo de renderização do componente pai, pois no JavaScript, `{}` não é igual a `{}` por referência. Para resolver isso:
*   Usei o `useCallback` nas funções de manipulação de drag-and-drop enviadas aos cards.
*   Usei o `useMemo` para calcular e filtrar a lista de clientes, garantindo que o array de clientes filtrados só seja recalculado se a lista original ou a pesquisa mudarem:

```typescript
const handleDragStart = useCallback((e: React.DragEvent, id: number) => {
  e.dataTransfer.setData('text/plain', id.toString());
}, []);

const clientesFiltrados = useMemo(() => {
  return clientes.filter(c => c.nome.toLowerCase().includes(searchQuery.toLowerCase()));
}, [clientes, searchQuery]);
```

---

## 🚦 2. Debouncing em Inputs de Busca

Disparar o filtro de dados na mudança de cada caractere digitado (evento `onChange`) em grandes listas degrada o desempenho. 

### Solução: Debounce de 300ms
Implementei um atraso (debounce) de 300 milissegundos no componente `CRMToolbar`. O estado local do input muda instantaneamente ao digitar (garantindo digitação fluida), mas o estado de pesquisa global que filtra a lista só é atualizado após o usuário parar de digitar por 300ms:

```typescript
import React, { useState, useEffect } from 'react';

interface ToolbarProps {
  onSearchChange: (q: string) => void;
}

export const CRMToolbar = ({ onSearchChange }: ToolbarProps) => {
  const [localSearch, setLocalSearch] = useState('');

  useEffect(() => {
    const timer = setTimeout(() => {
      onSearchChange(localSearch);
    }, 300);

    return () => clearTimeout(timer); // Limpa o timer anterior se digitar outro caractere
  }, [localSearch, onSearchChange]);

  return (
    <input
      type="text"
      value={localSearch}
      onChange={(e) => setLocalSearch(e.target.value)}
      placeholder="Buscar cliente..."
    />
  );
};
```

---

## 🎨 3. Estabilidade Visual do Layout (Cumulative Layout Shift - CLS)

Telas brancas seguidas de surgimento abrupto de dados causam deslocamento visual de layout (CLS), o que é penalizado em métricas de SEO e confunde o usuário.

### Solução: Skeletons e Contêineres de Altura Fixa
*   **Skeletons Animados (`LoadingState`):** Enquanto os dados estão sendo buscados na API, renderizamos uma estrutura cinza estática que possui exatamente o mesmo tamanho e dimensões dos futuros cards de clientes, suavizados com uma animação de pulso (`animate-pulse`).
*   **Contêineres Flex-1 com Overflow:** Garante que a estrutura da página (menus laterais, barra de tarefas, dashboard) permaneça no mesmo lugar enquanto apenas a área de dados faz o scroll interno, eliminando saltos de tela e mantendo a experiência de uso extremamente fluida.
