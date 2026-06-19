# E-commerce Data Integration & Analytics

[![Python](https://img.shields.io/badge/Python-3.x-blue?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![SQLite3](https://img.shields.io/badge/SQLite3-Storage-003B57?style=flat&logo=sqlite&logoColor=white)](https://www.sqlite.org/index.html)
[![Pandas](https://img.shields.io/badge/Pandas-Data%20Manipulation-150458?style=flat&logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Looker Studio](https://img.shields.io/badge/Looker%20Studio-Dashboard-4285F4?style=flat&logo=google&logoColor=white)](https://lookerstudio.google.com/)

Este projeto aborda o desenvolvimento de um pipeline de integração de dados (*ETL*) para o cenário de e-commerce. O objetivo principal foi consolidar dados transacionais e cadastrais descentralizados utilizando consultas SQL via Python, preparando uma base unificada, consistente e tratada para alimentar um dashboard analítico focado em tomada de decisão.

> 📊 **Acesse o Dashboard Interativo:** [https://datastudio.google.com/s/iQyMU1fJDMI]
---

## 📌 Contexto do Problema e Dados

No ambiente de e-commerce, as informações costumam residir em tabelas distintas. Para realizar análises demográficas de consumo, foi necessário cruzar as seguintes fontes de dados brutos:

1. **Tabela de Transações (`TB_TRANSACOES_PROJETO_ECOMM.csv`):** Contém 367 registros de compras realizadas, detalhando a categoria do produto (`Category`), preço (`Price`), tipo de cartão utilizado (`Card Type`) e o identificador do cliente (`id_client`).
2. **Tabela de Dados Pessoais (`TB_CLIENTES_PROJETO_ECOMM.csv`):** Contém o cadastro de 175 clientes únicos, especificando localização (`state_name`), primeiro nome (`First_name`), gênero (`Gender`) e profissão (`Job_Title`).

**Chave de Relacionamento:** A conexão lógica entre as tabelas foi estabelecida através do campo comum `id_client`.

---

## 🛠️ Tecnologias e Ferramentas Utilizadas

* **Python:** Linguagem central para automação do script.
* **Pandas:** Manipulação de dados, leitura de arquivos delimitados e exportação do resultado final.
* **SQLite3:** Engine de banco de dados relacional embarcado, utilizado para simular um ambiente de data warehouse e executar queries estruturadas.
* **Power BI:** Ferramenta utilizada para o mapeamento visual dos indicadores (*Data Storytelling*).

---

## ⚙️ Arquitetura do Pipeline (Passo a Passo)

### 1. Carga e Armazenamento em Banco Relacional
Os arquivos CSV foram carregados em memória via Pandas e indexados temporariamente em tabelas SQL dentro do banco de dados relacional `projeto.db`.

### 2. Análise de Consistência e Decisão Técnica (SQL)
Antes da junção, foi executado um diagnóstico de volumetria nas tabelas:
* A tabela de transações possuía **241 clientes únicos** realizando compras.
* A tabela de cadastros possuía apenas **175 clientes registrados**.

**Estratégia de Integração:** Optou-se pela aplicação de um **`INNER JOIN`**. A escolha se justifica pela necessidade de garantir a integridade analítica do Dashboard: cruzar transações sem correspondência cadastral geraria registros com dados demográficos nulos (órfãos), o que prejudicaria a segmentação por estado ou profissão. 

Com o `INNER JOIN`, consolidamos **296 transações de 170 clientes cadastrados**, extraindo o máximo de histórico de compras com riqueza de dados cadastrais válidos.

```sql
SELECT *
FROM tb_transacoes AS A
INNER JOIN tb_clientes AS B
ON A.Id_client = B.Id_client
ORDER BY A.Id_client ASC
```

### 3.  Exportação
O resultado da query foi convertido de volta para um dataframe do Pandas e exportado no formato dados_ecommerce_final.csv, servindo como uma fonte única de verdade (Single Source of Truth) limpa e otimizada.

### 📈 Próximos Passos & Insights do Dashboard

A base tratada foi conectada ao Power BI para monitorar indicadores-chave de performance (KPIs), tais como:

**Faturamento por Categoria de Produto:**
Identificar quais departamentos geram maior receita.

**Perfil do Consumidor:** Cruzamento de variáveis como Gênero e Profissão com o volume de compras.

**Distribuição Geográfica:** Mapeamento de vendas por Estado para otimização de logística e marketing direcionado.
