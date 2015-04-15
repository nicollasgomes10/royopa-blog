{
"title" : "Calculando o Value at Risk de uma ação no Python",
"author":"Royopa",
"date":"15-04-2015",
"tag":"python, finance",
"slug" : "calculando-o-value-at-risk-de-uma-acao-no-python",
"category":"finance"
}

Uma madrugada que utilizei para juntar o conhecimento que estou adquirindo fazendo um curso ppreparatório para a [Certificação de Especialista em Investimentos Anbima (CEA)](http://certificacao.anbid.com.br/cea.asp) e também aprendendo a [linguagem de programação Python](https://www.python.org/) resolvi fazer alguns testes e pesquisas para calcular o [Value at Risk (VaR)](http://pt.wikipedia.org/wiki/Value_at_Risk) de uma ação pegando os dados do site [Quandl](https://www.quandl.com/).

Existe um ótimo [artigo sobre o Quandl do Wilson Freitas](http://wilsonfreitas.github.io/posts/2014-03/quandl-uma-otima-forma-de-obter-dados-estruturados-em-python.html), um cara que é fera em Python e ciência de dados. Tinha visto o blog dele na semana passada e gostado bastante das análises. Por coincidência assisti uma [palestra dele](http://www.slideshare.net/wfreitas/analise-dos-campeoes-da-corrida-de-sao-silvestre-com-python) no encontro GrupySP do dia 11/04/2015.

Fazendo algumas pesquisas encontrei um [artigo legal](http://gosmej1977.blogspot.com.br/2013/06/value-at-risk.html) sobre como calcular o VaR usando Python, então resolvi traduzi-lo e adaptá-lo conforme as minhas necessidades, segue abaixo o texto:

De acordo com a Wikipédia, Value at Risk (VaR) é um método para avaliar o risco em operações financeiras. O VaR resume, em um número, o risco de um produto financeiro ou o risco de uma carteira de investimentos , de um montante financeiro. Esse número representa a pior perda esperada em um dado horizonte de tempo e é associado a um intervalo de confiança. 

Normalmente o VaR é calculado com 95%, 97,5% ou 99% de confiança. Este nível de confiança nos indica que é esperada perda maior que a calculada pelo VaR. Assim, ao utilizar 99% de confiança, espera-se que a cada 100 observações do VaR, em pelo menos 1 vez a perda do investimento financeiro seja superior à perda estimada no cálculo do VaR.

Assim, o VaR diário de 95% é uma quantidade negativa de dinheiro que separa os nossos resultados dos outros 5% piores resultados. Então, sabemos que 95% dos nossos dias será melhor do que esse número e 5% pior.

Existem várias técnicas para o cálculo do VaR. Estas técnicas podem ser divididas em dois grandes grupos: VaR Paramétrico e VaR Não Paramétrico (Simulação). O VaR Paramétrico baseia-se no conhecimento prévio de uma distribuição estatística (Ex.: Curva Normal) para fazer o cálculo das perdas financeiras com base em hipótese de comportamento da distribuição de probabilidades dos retornos dos ativos. O VaR Não Paramétrico não faz hipótese alguma sobre a distribuição de probabilidade dos retornos dos ativos. Nestas técnicas (Ex.: Simulação Histórica, Simulação de Monte Carlo) são utilizadas a história dos próprios retornos para obtenção de informações sobre as perdas financeiras.

O VaR deve ser sempre associado a:
    Uma moeda (valor monetário)
    Um intervalo de tempo
    Uma probabilidade com que a perda será percebida


Ex.: "O VaR da minha carteira, para 1 dia e com 95% de confiança é de R$ 100.000,00" onde:

- "...para 1 dia": significa que o cálculo do VaR considerou a hipótese de maior perda para acontecer no próximo dia
- "...com 95% de confiança": significa que para cada 100 dias é esperado que em 5 dias a perda realizada seja maior do que a prevista pelo VaR
- "...é de R$ 100.000,00": Montante financeiro máximo de perda esperada

Tendo em conta que a distribuição dos preços muda com o tempo, não se pode realmente saber o VaR, mas pode-se estimá-lo.

O código a seguir usa python3 o [pandas](http://pandas.pydata.org/), uma biblioteca bastante útil para manipular dados financeiros e também o [matplotlib](http://matplotlib.org/) para geração dos gráficos.

 ```python
# Choose a time period 
d1 = datetime.datetime(2001, 1, 1)
d2 = datetime.datetime(2012, 1, 1)

#get the tickers
stock = "PETR4.SA"
price = DataReader(stock, "yahoo",d1,d2)['Adj Close']
price = price.asfreq('B').fillna(method='pad')

ret = price.pct_change()

#choose the quantile
quantile=0.05
#the vol window
volwindow=50
#and the Var window for rolling 
varwindow=250
```

Estou usando a ação PETR4.SA mas isso não importa, caso você queira mudar basta alterar o parâmetro stock, de acordo com o código da ação no [Yahoo Finance](http://finance.yahoo.com/). A volatilidade vai ser estimada utilizando uma janela 


Eu optar por usar 3M para o meu estoque, mas isso não importa. A volatilidade vai ser estimada utilizando uma janela móvel e também vou testar usando uma janela móvel com estimativas quantiles.
Note que a janela para quantiles precisa ser grande enquanto tentamos estimar o quantil de 5% (ou 1%) por isso precisamos garantir que temos observações suficientes.
A janela para a volatilidade não deve ser muito grande pois a volatilidade muda bem rápido.

A linha "price = price.asfreq('B').fillna(method='pad')" tem a amostra de dados de todos os dias úteis, se o preço tiver faltando então preço anterior é copiado.

Depois os próprios cálculos
```python
#VaR simples usando todos os dados
unnormedquantile=pd.expanding_quantile(ret,quantile)

#similar usando uma janela móvel
unnormedquantileR=pd.rolling_quantile(ret,varwindow,quantile)

#nós também podemos normalizar o retorno por parte da vol
vol=pd.rolling_std(ret,volwindow)*np.sqrt(256)
unitvol=ret/vol

#and get the expanding or rolling quantiles
Var=pd.expanding_quantile(unitvol,quantile)
VarR=pd.rolling_quantile(unitvol,varwindow,quantile)

normedquantile=Var*vol
normedquantileR=VarR*vol
```

Using rolling functions of Panda makes this rather simple. Rolling_quantile calculates the quantile on a rolling window. expanding_quantile however estimates the quantiles using all the data available until the considered date.

Remember we got a serie of prices from Yahoo, then for each date in the serie we have an estimate of the VaR based on past data, not on all the data as it would be cheating.

We can then plot it: 

```python
ret2=ret.shift(-1)

courbe=pd.DataFrame({'returns':ret2,
              'quantiles':unnormedquantile,
              'Rolling quantiles':unnormedquantileR,
              'Normed quantiles':normedquantile,
              'Rolling Normed quantiles':normedquantileR,
              })
courbe.plot()
plt.show()
```

It shows the returns from 3M and the different VaR we computed. 
It is a pretty dense graph so I don't reproduce it here but you won't have issue reproducing this.

We see the normed VaR is much more variable as it follows the volatility. Finally we can judge the efficiency of our calculations. The VaR 95% should be broken 5% of the time, the following code tests this:

```python
courbe['nqBreak']=np.sign(ret2-normedquantile)/(-2) +0.5
courbe['nqBreakR']=np.sign(ret2-normedquantileR)/(-2) +0.5
courbe['UnqBreak']=np.sign(ret2-unnormedquantile)/(-2) +0.5
courbe['UnqBreakR']=np.sign(ret2-unnormedquantileR)/(-2) +0.5


nbdays=price.count()

print 'Number of returns worse than the VaR'
print 'Ideal Var                : ', (quantile)*nbdays
print 'Simple VaR               : ', np.sum(courbe['UnqBreak'])
print 'Normalized VaR           : ', np.sum(courbe['nqBreak'])
print '---------------------------'
print 'Ideal Rolling Var        : ', (quantile)*(nbdays-varwindow)
print 'Rolling VaR              : ', np.sum(courbe['UnqBreakR'])
print 'Rolling Normalized VaR   : ', np.sum(courbe['nqBreakR'])
```

The rolling VaR uses a rolling window approach for the quantile estimation, but it need some time before the window is filled so the number of days to test it is different. From these numbers alone, the simple VaR is better, closer to the 5% of VaR breaks. but from the graph, it seem very static and independent from the market and as a measure of risk that is not something I would desire.

One could improve on this by changing the way I calculate the volatility. I use a simple estimator, one could use one based on open/high/low/close instead or a GARCH framework.
