# 🌟 Modelagem e Transformação de Dados com DAX no Power BI
### Star Schema com Financial Sample

**Autor:** Arthur Haerdy Jr  
**Repositório:** [dio-powerbi-financials-star_schema](https://github.com/ahaerdy/dio-powerbi-financials-star_schema)  
**Bootcamp:** NTT Data — Engenharia de Dados com Python  
**Instrutora:** Juliana Mascarenhas (Tech Education Specialist / Cientista de Dados)

---

## 📋 Descrição do Projeto

O objetivo deste projeto é transformar uma tabela plana única — a **Financial Sample** — em um modelo de dados relacional otimizado no formato **Star Schema (Esquema Estrela)**, utilizando Power Query para as transformações e DAX para a criação da tabela de calendário.

---

## 📊 Sobre a Fonte de Dados — Financial Sample

A tabela original `financial_sample.xlsx` possui **700 linhas** e **16 colunas**, cobrindo o período de **setembro/2013 a dezembro/2014**.

| Coluna | Tipo | Descrição |
|---|---|---|
| Segment | Texto | Segmento de mercado (Government, Midmarket, Channel Partners, Enterprise, Small Business) |
| Country | Texto | País (Canada, Germany, France, Mexico, USA) |
| Product | Texto | 6 produtos: Carretera, Montana, Paseo, Velo, VTT, Amarilla |
| Discount Band | Texto | Faixa de desconto: None, Low, Medium, High |
| Units Sold | Decimal | Quantidade de unidades vendidas |
| Manufacturing Price | Inteiro | Preço de fabricação do produto |
| Sale Price | Inteiro | Preço de venda praticado |
| Gross Sales | Decimal | Vendas brutas |
| Discounts | Decimal | Valor do desconto aplicado |
| Sales | Decimal | Vendas líquidas |
| COGS | Decimal | Custo dos produtos vendidos |
| Profit | Decimal | Lucro da transação |
| Date | Data | Data da transação |
| Month Number | Inteiro | Número do mês |
| Month Name | Texto | Nome do mês |
| Year | Inteiro | Ano (2013 ou 2014) |

---

## 🗂️ Tabelas Criadas

### Financials_origem
Cópia oculta da tabela original usada como backup. Criada por duplicação da query `Financials` no Power Query, com carregamento desativado.

---

### D_Produtos
Tabela dimensão criada por **agrupamento** da tabela original pelo campo `Product`, consolidando estatísticas de vendas por produto.

| Coluna | Descrição |
|---|---|
| ID_Produto | Chave primária (coluna condicional) |
| Product | Nome do produto |
| Media_Unidades_Vendidas | Média de unidades vendidas |
| Media_Valor_Vendas | Média do valor de vendas |
| Mediana_Valor_Vendas | Mediana do valor de vendas |
| Max_Valor_Venda | Valor máximo de venda |
| Min_Valor_Venda | Valor mínimo de venda |

---

### D_Produtos_Detalhes
Tabela dimensão com informações cadastrais fixas dos produtos, com uma linha por produto.

| Coluna | Descrição |
|---|---|
| ID_Produto | Chave estrangeira para D_Produtos |
| Product | Nome do produto |
| Manufacturing Price | Preço de fabricação |

> Campos transacionais como `Sale Price` e `Units Sold` foram removidos pois variam por transação e pertencem à tabela fato.

---

### D_Descontos
Tabela dimensão com as faixas de desconto disponíveis por produto.

| Coluna | Descrição |
|---|---|
| ID_Produto | Chave estrangeira para D_Produtos |
| Product | Nome do produto |
| Discount Band | Faixa de desconto (None, Low, Medium, High) |

---

### D_Detalhes
Tabela dimensão com informações contextuais de vendas que não foram contempladas nas demais dimensões.

| Coluna | Descrição |
|---|---|
| SK_Detalhes | Chave substituta |
| Segment | Segmento de mercado |
| Country | País |
| Sales | Vendas líquidas |
| COGS | Custo dos produtos vendidos |
| Profit | Lucro |
| Gross Sales | Vendas brutas |
| Discounts | Valor do desconto |

---

### D_Calendário
Tabela de datas criada via **DAX** com a função `CALENDAR()`, cobrindo todo o período dos dados. Marcada como **Tabela de Data** no modelo para habilitar funções de inteligência de tempo.

```dax
D_Calendário = CALENDAR(DATE(2013, 1, 1), DATE(2014, 12, 31))
```

Colunas calculadas adicionadas:

```dax
Ano = YEAR(D_Calendário[Date])
Mes = MONTH(D_Calendário[Date])
Nome_Mes = FORMAT(D_Calendário[Date], "MMMM")
Trimestre = "T" & QUARTER(D_Calendário[Date])
Ano_Mes = FORMAT(D_Calendário[Date], "YYYY-MM")
```

---

### F_Vendas (Tabela Fato)
Tabela central do Star Schema, contendo todas as transações com suas métricas e chaves de relacionamento.

| Coluna | Descrição |
|---|---|
| SK_ID | Chave substituta da transação (índice) |
| ID_Produto | Chave estrangeira para as dimensões de produto |
| Product | Nome do produto |
| Units Sold | Unidades vendidas |
| Sale Price | Preço de venda |
| Discount Band | Faixa de desconto |
| Segment | Segmento de mercado |
| Country | País |
| Profit | Lucro |
| Gross Sales | Vendas brutas |
| Sales | Vendas líquidas |
| COGS | Custo dos produtos vendidos |
| Date | Data da transação (chave para D_Calendário) |

---

## ⚙️ Etapas e Funcionalidades Utilizadas

### Power Query
- **Referência de query** — para criar tabelas derivadas sem duplicar dados
- **Duplicação de query** — para criar o backup Financials_origem
- **Remover outras colunas** — para selecionar apenas os campos de cada dimensão
- **Remover duplicatas** — para garantir unicidade nas tabelas dimensão
- **Agrupar por** — para criar métricas estatísticas na D_Produtos
- **Coluna condicional** — para gerar o ID_Produto com base no nome do produto
- **Coluna de índice** — para gerar chaves substitutas (SK_ID, SK_Detalhes)
- **Reordenar colunas** — para organizar os campos de cada tabela
- **Ativar/desativar carregamento** — para ocultar a tabela de backup do modelo

### DAX
- `CALENDAR()` — para gerar a tabela de datas
- `DATE()` — para definir o intervalo do calendário
- `YEAR()`, `MONTH()`, `QUARTER()` — para extrair partes da data
- `FORMAT()` — para formatar datas como texto (nome do mês, ano-mês)

---

## 🌟 Star Schema — Diagrama do Modelo

![Star Schema](star_schema.png)

### Relacionamentos

| Tabela Fato | Coluna | Tabela Dimensão | Coluna | Cardinalidade |
|---|---|---|---|---|
| F_Vendas | ID_Produto | D_Produtos | ID_Produto | *:1 |
| F_Vendas | ID_Produto | D_Produtos_Detalhes | ID_Produto | *:1 |
| F_Vendas | Discount Band | D_Descontos | Discount Band | *:1 |
| F_Vendas | Date | D_Calendário | Date | *:1 |

---

## 🛠️ Tecnologias Utilizadas

- **Power BI Desktop**
- **Power Query (M)**
- **DAX (Data Analysis Expressions)**
- **Excel (.xlsx) — fonte de dados**

---

## 📁 Arquivos do Repositório

```
├── financial_sample.xlsx       # Fonte de dados original
├── financials_star_schema.pbix # Arquivo Power BI com o modelo completo
├── star_schema.png             # Print do diagrama Star Schema
└── README.md                   # Este arquivo
```

---

## 📚 Referências

- [DIO — Bootcamp NTT Data Engenharia de Dados com Python](https://web.dio.me)
- [Microsoft — Documentação Power BI](https://learn.microsoft.com/pt-br/power-bi/)
- [Microsoft — Referência DAX](https://learn.microsoft.com/pt-br/dax/)



<center>▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒</center>



# Modelagem de Vendas Corporativas com Power BI - Star Schema

## 📌 Descrição do Projeto
Este repositório contém o desenvolvimento do desafio de modelagem de dados proposto no Bootcamp NTT DATA - Engenharia de Dados com Python. O projeto consiste em processar uma base de dados financeira bruta (`financial_sample.xlsx`) e estruturá-la seguindo as melhores práticas de Business Intelligence, utilizando o modelo **Star Schema** (Esquema Estrela).

O foco principal é a transição de uma tabela única (flat file) para um modelo multidimensional, visando otimizar a performance do dashboard e facilitar a criação de cálculos complexos via DAX.

## 📊 Especificação dos Dados Originais
A tabela abaixo descreve a estrutura da fonte de dados utilizada antes do processo de modelagem:

| Nome da Coluna | Tipo de Dado (Power BI) | Descrição do Conteúdo |
| :--- | :--- | :--- |
| **Segment** | Texto (String) | Segmento de mercado do cliente. |
| **Country** | Texto (String) | País onde a venda foi realizada. |
| **Product** | Texto (String) | Nome do produto vendido. |
| **Discount Band** | Texto (String) | Categoria do nível de desconto (None, Low, Medium, High). |
| **Units Sold** | Número Decimal (Float) | Quantidade de itens vendidos. |
| **Manufacturing Price** | Número Decimal / Moeda | Custo de fabricação por unidade. |
| **Sale Price** | Número Decimal / Moeda | Preço de venda unitário de tabela. |
| **Gross Sales** | Número Decimal / Moeda | Valor total bruto (Unidades x Preço de Venda). |
| **Discounts** | Número Decimal / Moeda | Valor total do desconto aplicado à venda. |
| **Sales** | Número Decimal / Moeda | Valor líquido das vendas (Gross Sales - Discounts). |
| **COGS** | Número Decimal / Moeda | Custo das Mercadorias Vendidas (Unidades x Custo Fab.). |
| **Profit** | Número Decimal / Moeda | Lucro líquido (Sales - COGS). |
| **Date** | Data (Date) | Data completa da transação. |
| **Month Number** | Número Inteiro (Integer) | Representação numérica do mês (1 a 12). |
| **Month Name** | Texto (String) | Nome do mês por extenso. |
| **Year** | Número Inteiro (Integer) | Ano da transação. |

## 🛠️ Etapas do Projeto

1. **Limpeza e Transformação:** processamento dos dados da fonte no Power Query para garantia de integridade das informações.
2. **Modelagem Multidimensional:** criação da tabela **Fato** (`F_Vendas`) e das tabelas de **Dimensão** (`D_Descontos`, `D_Detalhes`, `D_Produtos`).
3. **Medidas DAX:** criação da tabela dimensional de tempo (`D_Calendario`)
4. **Visualização:**  apresentação do modelo com a tabela fato centralizada, rodeada e filtrada pelas tabelas de dimensão.

## Relatório de Execução: Modelagem Dimensional Star Schema

### Processo de ETL e Transformação (Power Query)

#### Governança da Fonte
Desenvolvimento de KPIs essenciais como Total de Vendas, Lucro Total e métricas comparativas de tempo.
- Seguindo o roteiro do desafio, as tabelas foram criadas a partir de referências à tabela original para garantir a linhagem dos dados.
- A tabela original foi preservada como `Financials_origem`.
- **Configuração:** A carga desta tabela foi desabilitada para otimizar o consumo de memória do motor VertiPaq.

#### Criação das Tabelas de Dimensão

- Financials_origem (modo oculto – backup)
- D_Produtos (ID_produto, Produto, Média de Unidades Vendidas, Médias do valor de vendas, Mediana do valor de vendas, Valor máximo de Venda, Valor mínimo de Venda)
- D_Detalhes(ID_produtos, Discount Band, Sale Price,  Units Sold, Manufactoring Price)
- D_Descontos (ID_produto, Discount, Discount Band)
- D_Detalhes (Products, Country, Vendas_Brutas, Descontos, Vendas Líquidas, Custo_Mercadoria, Lucro)
- D_Calendário – Criada por DAX com calendar()
- F_Vendas (SK_ID , ID_Produto, Produto, Units Sold, Sales Price, Discount  Band, Segment, Country, Salers, Profit, Date (campos))

Detalhes dos agrupamentos da tabela **D_Detalhes**:

<p align="center">
  <img src="000-Midia_e_Anexos/2026-04-17-14-54-19.png" alt="" width="480">
</p>

### Modelagem de Dados e DAX

#### Inteligência de Tempo
A tabela **D_Calendario** foi criada do zero utilizando DAX para permitir filtros temporais dinâmicos e comparativos de período (Time Intelligence).

```DAX
D_Calendario = 
VAR DataMinima = MIN(F_Vendas[Date])
VAR DataMaxima = MAX(F_Vendas[Date])
RETURN
ADDCOLUMNS (
    CALENDAR(DataMinima, DataMaxima),
    "Ano", YEAR([Date]),
    "Mês n.º", MONTH([Date]),
    "Nome do Mês", FORMAT([Date], "MMMM"),
    "Trimestre", "T" & QUARTER([Date]),
    "Dia da Semana", FORMAT([Date], "DDDD"),
    "Mês/Ano", FORMAT([Date], "MMM/YYYY")
)
```

### Arquitetura Star Schema
No ambiente de modelagem, as relações foram configuradas com cardinalidade **1:N (Um para Muitos)** e direção de filtro único, onde as dimensões filtram a fato.

| Tabela | Função | Chave de Ligação |
| :--- | :--- | :--- |
| **F_Vendas** | Fato | SK_ID (PK) |
| **D_Produtos** | Dimensão | ID_produto |
| **D_Detalhes** | Dimensão | ID_produto |
| **D_Descontos** | Dimensão | Discount Band / Product |
| **D_Calendário** | Dimensão | Date |

<p align="center">
  <img src="000-Midia_e_Anexos/2026-04-17-15-06-24.png" alt="" width="1024">
</p>

## 🚀 Como Visualizar
1. Clone este repositório.
2. Certifique-se de ter o **Power BI Desktop** instalado.
3. Abra o arquivo `.pbix` (disponível na pasta principal) para explorar o modelo e os visuais.

---
Desenvolvido por [Arthur Haerdy Junior](https://github.com/ahaerdy).