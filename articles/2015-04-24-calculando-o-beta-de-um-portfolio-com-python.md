{
"title" : "Calculando o Beta de um Portfólio com Python",
"author":"Royopa",
"date":"24-04-2015",
"tag":"python, finance",
"slug" : "calculando-o-beta-de-um-portfolio-com-python",
"category":"finance"
}

Tradução e adaptação do artigo [Calculating Portfolio Beta with Python](https://composmachina.com/blog/2013/03/calculating-portfolio-beta-with-python/) do [Dave Tucker](https://composmachina.com/blog/).

A teoria
--------

O Índice Beta é um indicador que mede a sensibilidade de um ativo em relação ao comportamento de uma carteira que represente o mercado.

É a relação entre a variação do retorno de uma ação (ativo) e o Ibovespa (mercado), por exemplo.

Portanto, o Índice Beta é uma medida do risco que um investidor está exposto ao investir em um ativo em particular em comparação com o mercado como um todo.

A fórmula do Índice Beta é bem simples:

Beta = Covariância entre o Retorno do Ativo e do Mercado / Variância do Retorno do Mercado

Ou desta maneira:

![Fórmula do Índice Beta](http://hcinvestimentos.com/wp-content/uploads/2011/12/formula-beta.png "Fórmula do Índice Beta")

onde:

βa = Beta
ra = Retorno do Ativo
rp = Retorno do Portfólio (Também pode ser usado como rm = Retorno do Mercado)

O coeficiente β é usado para medir o risco não-diversificável, isto é, fatores de mercado que afetam todas as empresas, como guerra, inflação, crises internacionais, etc. Ou seja, quanto o ativo está sujeito às variações não controláveis do mercado e do ambiente. É um índice que mede a relação entre o retorno de um ativo e o retorno do mercado. Desta forma, o prêmio por risco será sempre multiplicado por este coeficiente, exigindo um prêmio maior por risco quanto maior a variação do ativo em relação à carteira de mercado.

O beta da carteira de ações padrão, IBOVESPA, é sempre igual a 1, uma vez que ela é a base para o cálculo comparativo. O beta desta carteira é o beta médio de todos os títulos disponíveis. Desta forma, concluímos:

β = 1 Ativo médio.

Sua variação tende a acompanhar perfeitamente o mercado. Quando o IBOVESPA valoriza 5%, o ativo valoriza na mesma proporção.

β < 1 Ativo defensivo.

Possui oscilações inferiores ao mercado e no mesmo sentido. Quando o IBOVESPA valoriza 5%, o ativo tende a valorizar menos do que 5%.

β > 1 Ativo agressivo.

Possui oscilações maiores do que o mercado e no mesmo sentido. Por exemplo, uma ação com β=2,0 tende a valorizar/desvalorizar o dobro do mercado (IBOVESPA).

Suponha-se que seja necessário fazer a escolha entre três ações que farão parte de um portfólio agressivo de médio prazo (até 1 ano). As ações são: VALE5, TNLP4 e ELPL5. Iremos determinar o risco do ativo a partir do índice beta.

Considere-se, para este exemplo, que o mercado encontra-se em alta, com boas perspectivas para o próximo ano.

Analisando a explicação sobre o índice beta fornecida acima, concluímos que devemos escolher ativos com betas superiores a 1, pois pertencem a ativos que possuem oscilações maiores do que a carteira. Tanto positivamente quanto negativamente.

O próximo passo, muito importante, é observar o período de análise do β. Este deve estar alinhado com a estratégia de investimento, isto é, para um investimento de médio prazo, devemos escolher o β de um período similar.

Fontes:
[O que é Índice Beta?](http://hcinvestimentos.com/2011/12/20/indice-beta/)
[Wikipédia](http://pt.wikipedia.org/wiki/%C3%8Dndice_beta)

Vamos a programação
-------------------

O código a seguir usa python3 e o [Numpy](http://numpy.scipy.org/), pacote para a linguagem Python que suporta arrays e matrizes multidimensionais, possuindo uma larga coleção de funções matemáticas para trabalhar com estas estruturas.

```python
#!/usr/bin/python

import numpy as np
from datetime import date
import Quandl


# Faz a consulta de dados históricos do Quandl e retorna um array Numpy
def get_prices(stock, startDate, endDate):
    # Retorna os últimos 252 registros de cotações
    data = Quandl.get(
        stock, trim_start=startDate,
        trim_end=endDate,
        returns="numpy",
        authtoken="m2atjgMb4x11YczvyR_Q"
    )

    return data


# Transforma o array de preços retornados pelo Quantl
# num array Numpy somente com os preços de fechamento (index)
def get_closing_prices(prices, index):

    # Preços de fechamento que serão os preços de fechamento diários
    closing_prices = []

    # Percorre cada linha (ou seja, dia a dia)
    for price in prices:
        # Adiciona o elemento preço de fechamento no array, depois
        # de convertê-lo para float
        closing_prices.append(float(price[index]))

    # Retorna os resultados como um array Numpy
    return np.array(closing_prices)


# Calcula o beta de uma ação dados os preços bem como os preços para o mercado
# como um todo (como um índice, Bovespa por exemplo). Os dois arrays devem ter o
# mesmo tamanho.
# O resultado é o beta arredondado com duas casas decimais
def calc_beta(stock, market):

    # Calcula e armazena o tamanho dos arrays porque eles serão usados
    # várias vezes
    stock_len = len(stock)
    market_len = len(market)

    # Cria zero arrays que serão preenchidos com os retornos das ações. Os
    # dados de retorno sempre serão uma unidade menor do que os arrays originais
    stock_ret = np.zeros(stock_len - 1)
    market_ret = np.zeros(market_len - 1)

    # Percorre cada preço dado
    for cur in range(stock_len - 1):

        # O retorno é igual ao preço atual dividido pelo preço anterior menos 1.
        # Devido a isso, vamos sempre começar com o segundo preço
        stock_ret[cur] = (stock[cur + 1] / stock[cur]) - 1
        market_ret[cur] = (market[cur + 1] / market[cur]) - 1

    # Beta é igual à covariância do ativo e dos retornos do mercado,
    # dividido pela variância dos retornos do mercado.
    # Além disso, estou arredondado o beta para duas casas decimais.
    return np.around(
        np.cov(stock_ret, market_ret)[0, 1] / np.var(market_ret), decimals=2)

# data atual
today = date.today()
# data de início para pegar os dados de mercado - 5 anos
startDate = date(today.year - 4, today.month, today.day)
startDate = '2011-12-12'
today = '2011-12-16'

# pega os dados do índice Bovespa
stock = 'YAHOO/INDEX_BVSP'
data = get_prices(stock, startDate, today)
# print(data)

# para os dados do YAHOO o índice do preço de fechamento ajustado é o 6
# ["Date","Open","High","Low","Close","Volume","Adjusted Close"]
closing_prices_stock = get_closing_prices(data, 6)
print(closing_prices_stock)

# pega os dados da ação PETR4
stock = 'GOOG/BVMF_PETR4'
data = get_prices(stock, startDate, today)
# print(data)

# para os dados do GOOGLE o índice do preço de fechamento é o 4
# ["Date","Open","High","Low","Close","Volume"]
closing_prices_market = get_closing_prices(data, 4)
print(closing_prices_market)

beta = calc_beta(closing_prices_stock, closing_prices_market)
print('O beta é = ', beta)

```

O código em Pyhton acima pode ser baixado no Gist, no endereço: (https://gist.github.com/royopa/a8af15e8cead604ff6c2)