# 🎛️ Centro de Controle do Segundo Cérebro

Bem-vindo ao seu painel central de produtividade. Este dashboard utiliza o plugin **Dataview** para indexar dinamicamente as notas, projetos e tarefas de todo o seu cofre.

---

## ⚡ Atalhos de Navegação Rápida

| 📥 Entrada | 📚 Conhecimento | 💻 Engenharia | 📖 Referências |
| :--- | :--- | :--- | :--- |
| [[00-Inbox\|Inbox (Capturas)]] | [[01-Estudos\|Banco de Estudos]] | [[02-Projetos\|Projetos Ativos]] | [[05-Documentacao\|Documentação]] |
| [[03-Ideias\|Banco de Ideias]] | | [[04-Snippets\|Snippets Rápidos]] | [[Mapa do conhecimento\|Mapa Geral]] |

---

## 🚀 Projetos Ativos & Progresso
A tabela abaixo lista os seus projetos e a data da última modificação de cada um de forma dinâmica:

```dataview
TABLE file.mtime AS "Última Modificação"
FROM "Segundo cerebro/02-Projetos"
SORT file.mtime DESC
LIMIT 5
```

---

## 📝 Estudos Recentes
Últimos tópicos e conceitos adicionados ao banco de aprendizados:

```dataview
LIST
FROM "Segundo cerebro/01-Estudos"
SORT file.mtime DESC
LIMIT 5
```

---

## 📌 Checklist Geral de Tarefas
Lista consolidada de todas as pendências (`- [ ]`) encontradas em suas notas de projetos ou estudos que ainda não foram concluídas:

```dataview
TASK
WHERE !completed
SORT file.mtime DESC
GROUP BY file.link
```

---

## 🧩 Últimos Snippets Salvos
Biblioteca rápida de snippets de código para reutilização:

```dataview
LIST
FROM "Segundo cerebro/04-Snippets"
SORT file.mtime DESC
LIMIT 5
```
