{
"title" : "Otimização de portfólio de Markovitz no Python",
"author":"Royopa",
"date":"21-04-2015",
"tag":"python, finance",
"slug" : "otimizacao-de-portfolio-de-markovitz-no-python",
"category":"finance"
}

Tradução e adaptação dos artigos abaixo:

(http://blog.quantopian.com/markowitz-portfolio-optimization-2/)
(http://work.ange.le.free.fr/works/MarkowitzPortfolio/MarkowitzPortfolio.pdf)
(http://www.rodrigofernandez.com.br/ecomp/ref/excel_markowitz.pdf)
(http://hcinvestimentos.com/2009/08/14/harry-markowitz-fronteira-eficiente/)

Introdução
----------

De acordo com a Wikipédia, o Modelo de Markowitz permite que se calcule o risco de uma carteira de investimentos, não importando se é composta por ações, opções, renda fixa ou qualquer outro ativo. Um ponto interessante é que usando o Modelo de Markowitz é possível construir carteiras de investimento em que o risco é inferior ao ativo de menor risco da carteira. Isto é, imagine uma carteira com PETR4 (suponha risco de 3% ao dia) e TAMM4 (risco de 4% ao dia) em que o risco da carteira é inferior ao ativo de menor risco - o que significaria dizer que posso investir em Petrobrás e Tam e ainda assim obter um risco menor que 3% ao dia.

Neste post você vai aprender sobre a idéia básica por trás de otimização de carteiras de Markowitz, bem como a forma de calcular em Python. Veremos também como criar um backtest simples que reequilibra seu portfólio da melhor forma.

Vamos começar usando dados aleatórios e só mais tarde usar dados de estoque reais. Esperamos que possa ajudá-lo a ter uma noção de como usar modelagem e simulação para melhorar a sua compreensão dos conceitos teóricos. Não se esqueça que a habilidade de um [algotrader](https://fernandonogueiracosta.wordpress.com/2012/09/06/seguidor-automatico-de-estrategias-financeiras-algotrader-real-people-real-money/) é colocar modelos matemáticos em código e este exemplo é ótimo para praticar.

Vamos começar com a importação de alguns módulos, que precisaremos mais tarde para produzir uma série de retornos normalmente distribuídos. O [***cvxopt***](http://cvxopt.org/) é um pacote para otimização convexa que pode ser facilmente fazer a instalação com comando ***sudo pip instalar cvxopt*** ou num sistema Ubuntu like ***sudo apt-get install python-cvxopt***.

Simulações
----------

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
import cvxopt as opt
from cvxopt import blas, solvers
import pandas as pd

np.random.seed(123)

# Turn off progress printing 
solvers.options['show_progress'] = False
```

Suponhamos que temos 4 ativos, cada um com uma série de 1000 retornos. Podemos usar a função ***numpy.random.randn*** para a amostra de retornos de uma distribuição normal.

```python
## NUMBER OF ASSETS
n_assets = 4

## NUMBER OF OBSERVATIONS
n_obs = 1000

return_vec = np.random.randn(n_assets, n_obs)
```

```python
plt.plot(return_vec.T, alpha=.4);
plt.xlabel('time')
plt.ylabel('returns')
```

```python
<matplotlib.text.Text at 0x7fa919b8c690>
```
