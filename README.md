# 📊 Projeto de Segmentação de Clientes e Análise de BI (RFM)

## Objetivo do Projeto
O principal objetivo deste projeto é simular o dia a dia de um Analista de Dados/BI, fornecendo uma visão geral do negócio (e-commerce) e, realizar uma análise de **Segmentação de Clientes** utilizando a metodologia RFM (Recência, Frequência e Valor Monetário).

O foco é transformar dados brutos de transações em *insights* acionáveis para estratégias de marketing e retenção.

## 💾 Dataset Utilizado
* **Nome:** Online Retail II UCI
* **Fonte:** Kaggle: https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci
* **Descrição:** Contém dados de transações de uma loja de varejo online baseada no Reino Unido entre 01/12/2009 e 09/12/2011.

---

## ⚠️ Desafios do Dataset e Decisões Metodológicas

A base de dados apresentou desafios de qualidade que exigiram decisões específicas no tratamento:

1.  **Dados Geográficos Desconhecidos:** Uma parcela significativa das transações estava associada a países "Unknown" (Desconhecidos).
    * **Decisão:** Optei por **manter e agregar** esses dados, criando a métrica `Receita Identificada` e alertando sobre a limitação nas análises geográficas, para evitar que uma grande porção da receita total fosse descartada.
2.  **Tratamento de Outliers:** O *dataset* possui valores extremos (outliers) em Frequência e Valor.
    * **Decisão:** Os outliers foram **mantidos** no cálculo do Valor Monetário, pois representam uma parte significativa da Receita Total. A remoção poderia gerar uma distância artificial dos dados e impactar a acurácia da análise financeira do negócio.
3.  **Consistência de Dados:** Remoção de transações com quantidades ou preços negativos/nulos (considerados erros de registro).

---

## 🛠️ Detalhes Técnicos e Metodologia

### 1. ETL e Limpeza de Dados (Power Query)
A fase de *Extract, Transform, Load* (ETL) foi realizada integralmente no Power Query (M Language) e focou nos seguintes passos de tratamento:

* **Tratamento de Nulos:** Eliminação de registros com valores nulos críticos (e.g., `CustomerID`).
* **Tipagem de Dados:** Ajuste dos tipos de dados para otimização no modelo.
* **Criação de Dimesões:** Geração das tabelas de Dimensão (`DimTempo`, `DimProduto`, `DimCliente`) e da Tabela Fato (`FatoVendas`).
* **Nova Métrica:** Criação da coluna de `Receita Total` por transação.

### 2. Modelagem de Dados (Star Schema)
O modelo de dados segue o padrão **Star Schema**, otimizado para consultas de BI:

* **Fato:** `FatoVendas` (conectada às dimensões).
* **Dimensões:** `DimTempo`, `DimProduto`, `DimCliente`.
* **Exceção:** A tabela `DimSegmento` (que contém a descrição de cada segmento RFM) está conectada diretamente à `DimCliente` (modelo Snowflake simplificado) para atribuir o nome do segmento.

### 3. Análise e Segmentação RFM (DAX)
A segmentação é a parte central do projeto. Os clientes e receitas não idendificados foram retirados dessa análise. Ela foi implementada com colunas e medidas calculadas na linguagem DAX, com base nas decisões de negócio:

* **Cálculo dos Scores (R, F, V):**
    * **Recência (R) e Valor (V):** Segmentados usando o método de **Quintis** (`PERCENTILE.EXC`).
    * **Frequência (F):** Implementada com uma lógica **híbrida (Tiers fixos + Quartis)**: Clientes com até **2 compras** recebem Score **1**, e os scores restantes são distribuídos por **Quartis**.
* **Mapeamento de Segmentos:** Uma tabela (`DimSegmento`, criada no Excel e importada) foi usada para mapear as combinações de Scores RFV para nomes de segmentos.

### 4. Principais Métricas de BI (DAX)
Diversas métricas-chave de desempenho (KPIs) foram desenvolvidas no DAX para a visão geral do negócio:

* **Receita Total**
* **LTV (Lifetime Value)**
* **Total de Pedidos**
* **Ticket Médio**
* **Clientes Identificados**
* **Receita Identificada**

## 🖼️ Visualizações (Dashboard)
<img width="1479" height="831" alt="Captura de tela 2025-10-23 141112" src="https://github.com/user-attachments/assets/a016809e-4e44-468f-81a7-80d07d9f390b" />


<img width="1481" height="832" alt="Captura de tela 2025-10-22 220755" src="https://github.com/user-attachments/assets/109803ac-1171-4108-820b-a1f603f69af0" />




