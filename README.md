# Modelagem de Dashboard de E-commerce com Power BI - Star Schema

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
1. **Limpeza e Transformação:** Processamento dos dados no Power Query para garantir a integridade das informações.
2. **Modelagem Multidimensional:** - Criação da tabela **Fato** (`f_Vendas`).
   - Criação das tabelas de **Dimensão** (`d_Produtos`, `d_Calendario`, `d_Segmentos`, `d_Localidade`).
3. **Criação de Medidas DAX:** Desenvolvimento de KPIs essenciais como Total de Vendas, Lucro Total e métricas comparativas de tempo.
4. **Visualização:** Construção de um Dashboard interativo com filtros e visuais que facilitam a tomada de decisão.

## 🚀 Como Visualizar
1. Clone este repositório.
2. Certifique-se de ter o **Power BI Desktop** instalado.
3. Abra o arquivo `.pbix` (disponível na pasta principal) para explorar o modelo e os visuais.

---
Desenvolvido por [Arthur Haerdy Junior](https://github.com/ahaerdy).