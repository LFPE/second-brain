# 🎨 Design System & Workspace CRM — JurisHub

O frontend do JurisHub segue rigorosamente os padrões estéticos de aplicações SaaS modernas de alto padrão (Premium SaaS UI/UX).

---

## Estrutura do Design System

Os tokens de design estão definidos de forma imutável em `src/client/design-system/`:
*   `colors.ts`: Configura cores escuras de fundo (`colors.bg`), containers (`colors.card`) e cores de destaque e foco em tom âmbar (`colors.accent` / `colors.accentHover`). Define a paleta de sinalizações de status financeiro (LEDs piscantes para inadimplentes).
*   `motion.ts`: Centraliza tempos e easings padrões para o Framer Motion. Configura a curva de transição `easeOutExpo` (`[0.16, 1, 0.3, 1] as const`) para micro-animações rápidas de 180ms e 240ms.
*   `radius.ts` & `shadow.ts`: Bordas arredondadas generosas (`rounded-2xl` / `rounded-3xl`) e sombras profundas.

---

## Otimizações de Renderização no CRM

Para garantir respostas rápidas a interações pesadas de arrastar e soltar (drag and drop) no quadro Kanban do CRM:
1.  **Memoização de Cards (`React.memo`):** Os cards de clientes (`KanbanCard.tsx`) evitam re-renderizações desnecessárias comparando de forma rasa as suas propriedades.
2.  **Referência com `useCallback` e `useMemo`:** As callbacks de drag and drop são encapsuladas para evitar instanciamento redundante em ciclos de atualização, e filtros de buscas textuais utilizam memoização de dados calculados.
3.  **Debounce de Input:** A barra de busca no `CRMToolbar.tsx` aguarda 300ms de ociosidade na digitação para disparar o filtro de dados na lista principal, poupando CPU do navegador.
4.  **Estabilidade de Layout (CLS):** Uso de Skeletons estruturados com animação de pulso (`animate-pulse`) durante o carregamento de dados da API, prevenindo desvios bruscos de layout.
