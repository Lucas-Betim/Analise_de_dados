# Análise de Cancelamento de Clientes com Python

## Sobre o projeto

Este projeto tem como objetivo analisar uma base de clientes e identificar os principais fatores relacionados ao cancelamento de serviços.

A empresa analisada possui uma grande base de clientes e observou um número elevado de cancelamentos. A partir dos dados disponíveis, foi realizada uma análise exploratória para entender quais características e comportamentos estavam mais associados ao churn e quais ações poderiam ajudar a reduzir esse índice.

O projeto foi desenvolvido utilizando **Python, Pandas e Plotly**.

---

## Objetivo

Responder principalmente às seguintes perguntas:

- Qual é a taxa atual de cancelamento?
- Quais características dos clientes estão mais relacionadas ao churn?
- Existem comportamentos que indicam maior risco de cancelamento?
- Quais ações a empresa poderia tomar para reduzir a taxa de churn?

---

## Tecnologias utilizadas

- Python
- Pandas
- Plotly
- Jupyter Notebook

---

## Base de dados

A base utilizada contém aproximadamente **50 mil registros** e informações como:

- Idade
- Sexo
- Tempo como cliente
- Frequência de utilização
- Número de ligações para o call center
- Dias de atraso no pagamento
- Tipo de assinatura
- Duração do contrato
- Total gasto
- Meses desde a última interação
- Status de cancelamento

A variável `cancelou` representa o status do cliente:

- `0` → Cliente ativo
- `1` → Cliente que cancelou

---

# Etapas da análise

## 1. Importação dos dados

A base de dados foi importada utilizando a biblioteca Pandas.

```python
import pandas as pd

tabela = pd.read_csv("cancelamentos.csv")
```

O Pandas permite carregar, manipular e analisar grandes conjuntos de dados de forma eficiente.

---

## 2. Exploração inicial da base

Inicialmente foram analisadas as informações disponíveis no dataset para identificar:

- colunas existentes;
- tipos de dados;
- valores ausentes;
- possíveis informações irrelevantes para a análise.

A coluna `CustomerID`, por exemplo, foi removida porque funciona apenas como identificador do cliente e não contribui diretamente para a análise de churn.

```python
tabela = tabela.drop(columns="CustomerID")
```

---

## 3. Tratamento dos dados

Foi utilizado o método `info()` para verificar os tipos das colunas e identificar possíveis valores ausentes.

```python
tabela.info()
```

Foram encontrados poucos registros incompletos em relação ao tamanho total da base.

Essas linhas foram removidas:

```python
tabela = tabela.dropna()
```

Após o tratamento, a base permaneceu com aproximadamente **49.996 registros válidos**.

---

# Análise inicial do churn

A primeira análise teve como objetivo identificar a proporção de clientes ativos e cancelados.

```python
tabela["cancelou"].value_counts()
```

Resultado:

- Clientes cancelados: **28.393**
- Clientes ativos: **21.603**

Em seguida foi calculada a proporção:

```python
tabela["cancelou"].value_counts(normalize=True)
```

A taxa inicial de cancelamento encontrada foi de aproximadamente:

**56,79%**

Enquanto aproximadamente:

**43,21%**

dos clientes permaneciam ativos.

Esse resultado demonstra um cenário de churn bastante elevado e justifica uma investigação mais detalhada sobre suas possíveis causas.

---

# Análise exploratória

Para analisar a relação entre cada variável e o cancelamento, foram criados histogramas utilizando Plotly.

```python
import plotly.express as px

for coluna in tabela.columns:
    grafico = px.histogram(
        tabela,
        x=coluna,
        color="cancelou",
        text_auto=True
    )

    grafico.show()
```

Essa abordagem permite visualizar como clientes ativos e cancelados se distribuem em relação às diferentes variáveis da base.

---

# Principais padrões identificados

A análise exploratória indicou alguns comportamentos importantes.

## Duração do contrato

Os clientes com contrato **mensal** apresentaram uma concentração muito elevada de cancelamentos.

Isso sugere que contratos de maior duração podem aumentar a retenção dos clientes.

### Possível ação

Criar incentivos para migração de contratos mensais para:

- contratos trimestrais;
- contratos anuais;
- planos com benefícios por permanência.

---

## Ligações para o call center

Foi identificado um forte aumento nos cancelamentos entre clientes que realizaram muitas ligações para o suporte.

Clientes com mais de aproximadamente **4 contatos com o call center** apresentaram alta concentração de churn.

Isso pode indicar problemas como:

- baixa resolução no primeiro atendimento;
- problemas recorrentes;
- insatisfação com o serviço;
- demora na solução de solicitações.

### Possível ação

Criar um indicador de risco para clientes que entrarem em contato várias vezes.

Por exemplo:

> Ao atingir 3 contatos com o call center, o cliente poderia ser sinalizado para acompanhamento preventivo.

---

## Dias de atraso

Também foi observado aumento significativo de cancelamentos entre clientes com muitos dias de atraso no pagamento.

Clientes com mais de aproximadamente **20 dias de atraso** apresentaram alta concentração de churn.

### Possível ação

Criar ações preventivas antes que o atraso se torne crítico, como:

- lembretes automáticos;
- negociação de pagamento;
- contato preventivo;
- condições especiais de regularização.

---

# Simulação de melhorias

Com base nos principais padrões encontrados, foram aplicados filtros simulando um cenário em que alguns dos principais fatores de churn fossem reduzidos.

### Exclusão dos contratos mensais

```python
condicao = tabela["duracao_contrato"] != "Monthly"
tabela = tabela[condicao]
```

### Clientes com até 4 ligações para o call center

```python
condicao = tabela["ligacoes_callcenter"] <= 4
tabela = tabela[condicao]
```

### Clientes com até 20 dias de atraso

```python
condicao = tabela["dias_atraso"] <= 20
tabela = tabela[condicao]
```

Após a aplicação desses filtros, a distribuição de cancelamento passou para aproximadamente:

- **81,65% clientes ativos**
- **18,35% clientes cancelados**

---

# Observação importante

Essa redução de churn representa uma **simulação baseada em filtros sobre os dados existentes**, e não uma previsão causal de que essas ações reduziriam necessariamente a taxa real de cancelamento para 18,35%.

Para comprovar o impacto dessas medidas em um cenário real, seria necessário realizar análises adicionais, como:

- testes A/B;
- experimentos controlados;
- modelos estatísticos;
- modelos de Machine Learning;
- acompanhamento de métricas antes e depois das ações.

---

# Insights de negócio

A análise indica três áreas importantes para ações de retenção:

### 1. Incentivar contratos mais longos

Clientes mensais demonstraram maior associação com cancelamento.

### 2. Melhorar a experiência de atendimento

Um número elevado de contatos com o call center pode ser um sinal antecipado de insatisfação.

### 3. Criar alertas para atrasos de pagamento

Clientes com muitos dias de atraso possuem maior risco de churn e podem receber ações preventivas antes do cancelamento.

---

# 🚀 Possíveis evoluções do projeto

Como próximos passos, o projeto pode ser expandido com:

- análise de correlação entre variáveis;
- criação de dashboards;
- segmentação dos clientes;
- identificação automática de clientes de risco;
- criação de um modelo preditivo de churn;
- Logistic Regression;
- Random Forest;
- XGBoost;
- avaliação com Precision, Recall, F1-Score e ROC-AUC.

Uma evolução natural seria construir um modelo de Machine Learning capaz de receber os dados de um cliente e estimar sua probabilidade de cancelamento.

---

# Competências demonstradas

Este projeto demonstra conhecimentos em:

- Python
- Pandas
- Análise exploratória de dados
- Limpeza e tratamento de dados
- Manipulação de DataFrames
- Visualização de dados
- Plotly
- Identificação de padrões
- Geração de insights de negócio
- Análise de churn

---

## Conclusão

O projeto demonstrou como uma análise exploratória relativamente simples pode transformar dados brutos em informações úteis para tomada de decisão.

A partir da análise dos dados, foi possível identificar comportamentos associados ao cancelamento e propor possíveis estratégias de retenção.

Além da parte técnica, o projeto busca demonstrar a importância de conectar **análise de dados com problemas reais de negócio**.
