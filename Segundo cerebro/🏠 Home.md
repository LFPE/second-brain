# 🏠 Centro de Controle do Conhecimento (Knowledge OS)

Bem-vindo ao seu painel central de engenharia e produtividade. Este painel integra dinamicamente o status dos seus projetos, notas de estudo e checklists de tarefas do seu "Segundo Cérebro".

---

## ⚡ Atalhos Rápidos de Navegação

| 📥 Inbox & Diário | 💻 Projetos & Padrões | 📖 Wiki & Conceitos | 🛠️ Ferramentas & Guias |
| :--- | :--- | :--- | :--- |
| [[00-Inbox/Inbox\|📥 Inbox (GTD Capture)]] | [[02-Projetos/JurisHub/README\|⚖️ Projeto JurisHub]] | [[03-Conhecimento/Backend/Modelagem-PostgreSQL-Multi-Tenant\|🗄️ Multi-Tenancy]] | [[18-Playbooks/Deploy\|🚀 Playbook: Deploy]] |
| [[01-Diário/2026/Julho/16\|📅 Diário Técnico]] | [[02-Projetos/System-Tracker/README\|💻 Projeto Tracker]] | [[03-Conhecimento/IA/RAG-Busca-Vetorial-IA\|🤖 Pipeline RAG & IA]] | [[18-Playbooks/PR Checklist\|🔍 Playbook: Code Review]] |
| [[Decision Log\|✔️ Log de Decisões (ADR)]] | [[06-Standards/Coding Standards\|📋 Standards de Código]] | [[04-Arquitetura/DDD\|🏛️ Arquitetura: DDD]] | [[17-Diagrams/Flowchart\|📊 Gabaritos Mermaid]] |
| [[Stack\|🛠️ Stack de Tecnologias]] | [[06-Standards/Git Standards\|🐙 Standards de Git]] | [[04-Arquitetura/Clean-Architecture\|🏗️ Arquitetura Limpa]] | [[16-Snippets/SQL\|🧩 Snippets Reutilizáveis]] |

---

## 🚀 Projetos Ativos & Progresso
Esta tabela monitora a última modificação das notas dos seus projetos de forma automatizada:

```dataview
TABLE file.mtime AS "Última Modificação"
FROM "Segundo cerebro/02-Projetos"
SORT file.mtime DESC
LIMIT 5
```

---

## 📌 Checklist Geral de Pendências (Tasks)
Todas as tarefas em aberto (`- [ ]`) encontradas nas notas de projetos ou diários:

```dataview
TASK
WHERE !completed
SORT file.mtime DESC
GROUP BY file.link
```

---

## 📅 Histórico de Diários Recentes
Acompanhe os seus registros de progresso diário:

```dataview
LIST
FROM "Segundo cerebro/01-Diário"
SORT file.name DESC
LIMIT 5
```
