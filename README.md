# Análise Exploratória dos Dados do Airbnb

Airbnb é um marketplace que conecta pessoas que desejam alugar suas casas com pessoas que procuram acomodações naquela localidade. Desde 2008, hóspedes e anfitriões usam o Airbnb para expandir as possibilidades de viagem e apresentar uma maneira mais única e personalizada de experimentar o mundo. Hoje em dia, o Airbnb tornou-se um serviço único que é usado por todo o mundo. Os analistas de dados se tornam um fator crucial para a empresa que forneceu milhões de anúncios por meio do Airbnb. Essas listagens geram muitos dados que podem ser analisados ​​e usados ​​para segurança, decisões de negócios, compreensão do comportamento de clientes e fornecedores na plataforma, implementação de serviços adicionais inovadores, orientação de iniciativas de marketing e muito mais.

Principais perguntas a responder:

## Q1. Quantos atributos (variáveis) e quantas entradas o nosso conjunto de dados possui? Quais os tipos das variáveis?

```Python
import pandas as pd
df = pd.read_csv("./listings.csv")
df.head()

# Descrever o dicionário das variáveis do dataset; método columns

df.columns

# Caracterizar dataset

df.describe()
df.info()

# Verificar 'forma', quantidade de linhas e colunas

df.shape

# Caracterizar tamanho do dataset
# Identificar o volume de dados do DataFrame -> describe, length..etc(?)

print("Entradas:\t {}".format(df.shape[0]))
print("Variáveis:\t {}\n".format(df.shape[1]))

# Verificar as 5 primeiras entradas do dataset

display(df.dtypes)

```

## Q2. Qual a porcentagem de valores ausentes no dataset?

```Python
# df.isnull() -> retorna df com 'true' ou 'false' em cada célula
# se for incluído o método .sum(), é obtida a soma de nulos a cada coluna
df.isnull().sum()

(df.isnull().sum() / df.shape[0]).sort_values(ascending=False)

```

## Q3. Qual o tipo de distribuição das variáveis?

```Python
# Verificar distribuição das variáveis do dataset bruto
# Plotar histograma das variáveis numéricas -> df.hist(bins... figsize...)

df.hist(bins=15, figsize=(15,10))

```

## Q4. Há outliers presentes? Qual a correlação existente entre as variáveis

```Python
# Remover os *outliers* em um novo DataFrame
df_clean = df.copy()
df_clean.drop(df_clean[df_clean.price > 1500].index, axis=0, inplace=True)
df_clean.drop(df_clean[df_clean.minimum_nights > 30].index, axis=0, inplace=True)

# Remover `neighbourhood_group`, pois está vazio
df_clean.drop('neighbourhood_group', axis=1, inplace=True)

# Plotar o histograma para as variáveis numéricas
df_clean.hist(bins=15, figsize=(15,10))

# Verificar correlação existente entre variáveis
# Correlação vs causalidade -> https://www.tylervigen.com/spurious-correlations
# https://escoladedados.org/tutoriais/correlacao-nao-e-causalidade-mas-o-que-e-entao/

# Correlação significa que existe uma relação entre duas coisas.
# Neste contexto, busca-se a relação ou semelhança entre duas variáveis.

# Essa relação pode ser medida, e é função do coeficiente de correlação estabelecer
# qual a intensidade dela.
# Para identificar as correlações existentes entre as variáveis de interesse, vou:

# Criar uma matriz de correlação
# Gerar um heatmap a partir dessa matriz, usando a biblioteca seaborn

# Criar uma matriz de correlação

corr = df_clean[['price', 'minimum_nights', 'number_of_reviews', 'reviews_per_month',
    'calculated_host_listings_count', 'availability_365']].corr()

display(corr)

sns.heatmap(corr, cmap='RdBu', fmt='.2f', square=True, linecolor='white', annot=True)

```

## Q5. Qual o tipo de imóvel mais alugado no Airbnb?

```Python

# A coluna da variável room_type indica o tipo de locação que está anunciada no Airbnb.
# Se você já alugou no site, sabe que existem opções de apartamentos/casas inteiras,
# apenas o aluguel de um quarto ou mesmo dividir o quarto com outras pessoas.

# Contar a quantidade de ocorrências de cada tipo de aluguel, usando o método value_counts().

# Verificar maiores frequências -> qual o tipo de imóvel mais alugado no airbnb
# Verificar quantidade de cada tipo de imóvel disponível
# Verificar porcentagem de cada tipo de imóvel disponível


# mostrar a quantidade de cada tipo de imóvel disponível
df_clean.room_type.value_counts()

# mostrar a porcentagem de cada tipo de imóvel disponível
df_clean.room_type.value_counts() / df_clean.shape[0]

```

## Q6. Qual a localidade mais cara da cidade tal?

```Python
# Uma maneira de se verificar uma variável em função da outra é usando groupby().
# No caso, queremos comparar os bairros (neighbourhoods) a partir do preço de locação.

df_clean.groupby(['neighbourhood']).price.mean().sort_values(ascending=False)[:10]

# verificar localidade (bairro?) mais cara da cidade
# -> df.groupby(...).price.mean().sort_values()
# plotar os imóveis pela latitude-longitude

df_clean.plot(kind="scatter", x='longitude', y='latitude', alpha=0.4, c=df_clean['price'], s=8,
              cmap=plt.get_cmap('jet'), figsize=(12,8))

```
