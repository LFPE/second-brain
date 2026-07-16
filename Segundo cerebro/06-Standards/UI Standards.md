# UI Standards 🎨

Padrões estéticos e tokens visuais para garantir interfaces responsivas, consistentes e de alta fidelidade visual (Premium SaaS).

---

## 1. Regra de Contraste e Paleta
*   **Fundo de Aplicação Escura:** Utilizar predominantemente fundos em tons de cinza extremamente escuros (`bg-zinc-950` / `#09090b`) em vez de preto puro (`#000000`), mitigando cansaço visual.
*   **Foco / Ações Primárias:** Utilizar cores de destaque quentes e refinadas (como tons de Amber ou Violeta). Nunca usar azul/azul puro convencional sem saturação harmonizada.

---

## 2. Micro-animações e Motion
*   **Velocidade e Resposta:** Animações e transições na tela devem possuir duração máxima entre **180ms** e **240ms**. Transições mais lentas geram percepção de lentidão (lag) no sistema.
*   **Curva de Aceleração:** Utilizar predominantemente o easing `easeOutExpo` (`[0.16, 1, 0.3, 1]`) ou curvas bezier cúbicas equivalentes para aberturas de painéis laterais.

---

## 3. Estado de Componentes
*   **Skeletons:** Toda tela de carregamento deve simular a estrutura física dos elementos reais com Skeletons cinzas e animações de pulso sutil (`animate-pulse`). Evitar spinners isolados girando no centro da tela.
*   **Empty States:** Listagens vazias devem renderizar ilustrações exclusivas lineares minimalistas com texto instrucional e botão para ação direta de inserção.
