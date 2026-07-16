# 🧠 Lições Aprendidas — File Organizer

*   **Sempre prever colisão de nomes em scripts de cópia/movimentação:** Mover arquivos com nomes idênticos sem renomeá-los sobrescreve dados importantes de forma silenciosa.
*   **Tratamento de Strings no PowerShell é case-insensitive por padrão:** No entanto, para fins de consistência multiplataforma, é uma boa prática chamar `.ToLower()` nas extensões recuperadas.
*   **Uso de Hashtable acelera buscas:** Em vez de encadear múltiplos condicionais `if-else` ou cláusulas `switch`, mapear extensões em uma Hashtable deixa o código mais legível e rápido.
