# Como Utilizar os códigos?

É necessário clonar o repositório 
- `git clone https://github.com/Artur-Mac/LH_CD_ARTUR_ALBUQUERQUE_MACHADO/blob/master/notebooks/EDA.ipynb`
- No terminal digitar o comando pip install requirements.txt


#### **clean_columns.ipynb**: início da pipeline de processamento dos dados brutos, etapa em que é feito
- Remoção de coluna Unnamed: 0 (gerada automaticamente pelo pandas)
- Identificação inicial de colunas e comportamento macro
- Limpeza e formatação de colunas
- Identificação de comportamento atípicos (duplicatas em algumas variáveis, como título ou número de votos)
- Salvamento do df preprocessado

#### **feature_add.ipynb**: integração de dados com a api do TMDB e criação de novas features (além de alguns ajustes manuais)
- Adição de diversas novas colunas para auxiliar no df a partir da api (ex: detalhamento de data de lançamento, budget, popularidade, métrica do imdb atualizada diariamente, entre outras)
- Adição manual de valores em colunas com poucos valores faltantes (certificate e date_detail)
- Criação de features de mercado preenchidas por medianas ou outras taxas (apenas para análise exploratória, caso fossem colocados em aprendizado de máquina seria um vazamento de dados que poderia causar overfitting)
	
#### **EDA.ipynb**: documento que tem como objetivo utilizar das diversas variáveis novas e dados tratados para não só entender profundamente o mercado de filmes de sucesso, mas também responder a todas as dúvidas dos stakeholders
- Análise macro das variáveis
- Comportamento de correlações entre as colunas (para fazer isso foram utilizadas diversas funções matemáticas como: Pearson para numérica x numérica, ANOVA para categóricas x numéricas e V-Cramer que advém do chi-quadrado para compreender as relações entre variáveis categóricas)
- Compreensão de outliers relacionados a retorno de investimento (foi percebido que os filmes de baixo custo que podem alcançar o público mainstream são filmes de terror)
- Viu-se a necessidade de criar mais algumas features a partir das já existentes (como o índice de recomendação de filme)
- Análise temporal: entendimento da evolução dos valores de faturamento e lucro
- Checagem de sazonalidade baseada em meses
- Predição de gênero a partir do overview com processamento de linguagem natural básico

#### **pred.ipynb**: predição de nota do imdb a partir do df padrão (evitando vazamento de dados)
- Extremamente desafiante por conta do número de amostras e variabilidade dos dados
- Para o filme proposto, a nota encontrada foi de 8,9
- Entendimento de features que influenciam de forma situacional no modelo

---

### Colunas paralelas com o *dataframe* original

* **Gross, Revenue e Budget**: refletem informações parecidas. são extremamente úteis para entender o comportamento do mercado
- A melhor estratégia foi gerar novas colunas de interação. Mesmo tendo lógicas parecidas (e alta correlação) é importante manter o Gross original e criar um novo gross_fill que não vai ser usado no treinamento já que tem vazamento de dados (foi preenchido pela mediana)

* **Certificate**: está presente nos dois *df*.

  * Estratégia: discretizar, já que é uma variável ordinal.
  * Depois de verificar discrepâncias, decidiremos qual coluna manter. A princípio, a ideia é preservar a coluna original e complementar com a nova, garantindo ausência de nulos.

---

### Colunas novas que podem gerar bons insights

* **Budget**: custo de produção do filme.

  * De longe, a mais relevante entre as colunas adicionais, já que permite calcular métricas como **ROI** e **profit bruto**, fundamentais para a análise.

* **Keywords**: palavras-chave associadas ao filme.

  * Podem complementar o processamento de linguagem natural da coluna **Overview**, oferecendo insights sobre os tipos de tramas que atraem mais atenção do público.

* **Popularity**: índice de popularidade atribuído pelo IMDb.

  * Usa múltiplas métricas, e será interessante investigar como é calculado e qual impacto tem na performance do filme.

* **date\_details**: ano de lançamento original do filme.

  * Essencial para análises de sazonalidade. Exemplo: filmes de ação podem ter desempenho melhor em períodos de férias nos EUA.

* **Production\_Countries**: países de produção.

  * Útil para identificar padrões, como os contextos mais comuns de blockbusters.

---

### Observações importantes

1. **Previsão futura e vazamento de dados**

   * Tanto o *df* original quanto o processado trazem colunas que indicam performance **após o lançamento**.
   * Para construir modelos preditivos robustos (ou seja, capazes de estimar o sucesso de um filme **antes** dele estrear), é essencial excluir variáveis como número de votos, notas do IMDb, Metascore, Popularidade, entre outras que só existem depois da exibição.

2. **Não inclusão de novos filmes**

   * A estrutura do *df* foi preservada, mesmo que a amostragem seja pequena e enviesada.
   * Adicionar novos filmes poderia desviar do desafio inicial. Por outro lado, novas *features* apenas enriquecem a análise sem alterar a base original.

3. **Escopo e limitações**

    * Um fator extremamente importante é que para o treinamento as colunas originais tratadas que serão usadas para manter fidelidade com o desafio e evitar vazamento de dados (que estão presentes nas colunas que foram preenchidas com )
   * O projeto foca exclusivamente em lançamentos nos cinemas.
   * Questões ligadas ao mercado de streaming foram deixadas para trabalhos futuros, já que exigem dados específicos e análises próprias.
   * Além disso, há uma dimensão cronológica: o filme mais recente da base é de 2020, o que também deve ser considerado nas interpretações.


# Top_Film_Analysis

<a target="_blank" href="https://cookiecutter-data-science.drivendata.org/">
    <img src="https://img.shields.io/badge/CCDS-Project%20template-328F97?logo=cookiecutter" />
</a>

Análise de filmes em contexto do mercado

## Project Organization

```
├── LICENSE            <- Open-source license if one is chosen
├── Makefile           <- Makefile with convenience commands like `make data` or `make train`
├── README.md          <- The top-level README for developers using this project.
├── data
│   ├── external       <- Data from third party sources.
│   ├── interim        <- Intermediate data that has been transformed.
│   ├── processed      <- The final, canonical data sets for modeling.
│   └── raw            <- The original, immutable data dump.
│
├── docs               <- A default mkdocs project; see www.mkdocs.org for details
│
├── models             <- Trained and serialized models, model predictions, or model summaries
│
├── notebooks          <- Jupyter notebooks. Naming convention is a number (for ordering),
│                         the creator's initials, and a short `-` delimited description, e.g.
│                         `1.0-jqp-initial-data-exploration`.
│
├── pyproject.toml     <- Project configuration file with package metadata for 
│                         Top_Film_Analysis and configuration for tools like black
│
├── references         <- Data dictionaries, manuals, and all other explanatory materials.
│
├── reports            <- Generated analysis as HTML, PDF, LaTeX, etc.
│   └── figures        <- Generated graphics and figures to be used in reporting
│
├── requirements.txt   <- The requirements file for reproducing the analysis environment, e.g.
│                         generated with `pip freeze > requirements.txt`
│
├── setup.cfg          <- Configuration file for flake8
│
└── Top_Film_Analysis   <- Source code for use in this project.
    │
    ├── __init__.py             <- Makes Top_Film_Analysis a Python module
    │
    ├── config.py               <- Store useful variables and configuration
    │
    ├── dataset.py              <- Scripts to download or generate data
    │
    ├── features.py             <- Code to create features for modeling
    │
    ├── modeling                
    │   ├── __init__.py 
    │   ├── predict.py          <- Code to run model inference with trained models          
    │   └── train.py            <- Code to train models
    │
    └── plots.py                <- Code to create visualizations
```

--------
