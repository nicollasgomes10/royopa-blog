{
"title" : "O design pattern Proxy",
"author":"Royopa",
"date":"30-01-2016",
"tag":"design pattern, proxy",
"slug" : "o-design-pattern-proxy",
"category":"Design Patterns"
}

Intenção
----------

* Fornecer um espaço reservado para acesso controlado a um determinado objeto.
* Usar uma camada extra que permite um acesso inteligente, controlado e distribuído aos recursos do sistema.
* Usar um wrapper e delegate para encapsular o componente real que está sendo acessado.

Um proxy, em sua forma mais geral, é uma classe que funciona como uma interface para outra classe. A classe proxy poderia conectar-se a qualquer coisa: uma conexão de rede, um grande objeto em memória, um arquivo, ou algum recurso que é difícil ou impossível de ser duplicado.

O padrão Proxy é um aprimoramento na rotina de tratamento simples (ou ponteiro) usado como uma referência a um objeto: este ponteiro é substituído por um objeto proxy que se interpõe entre o cliente e a execução de trabalho real, acrescentando um gancho que pode ser explorado por muitos objetivos diferentes.

Classes participantes
------------------------

* **Subject** - A interface implementada pelo RealSubject representando seus serviços. A interface deve ser implementada pelo proxy de modo que o proxy possa ser usado em qualquer lugar onde o RealSubject possa ser usado.
* **Proxy** - Mantém uma referência que permite que o Proxy acesse o RealSubject. O Proxy implementa a mesma interface implementada pelo RealSubject então o Proxy pode ser substituído pelo RealSubject. Ele também controla o acesso para o RealSubject e pode ser responsável por sua criação ou remoção. Outras responsabilidades dependem do tipo de proxy.
* **RealSubject** - O objeto real que o proxy representa.

Tipos de Proxy
-----------------

O design pattern Proxy é aplicável quando existe a necessidade de controlar o acesso a um Objeto, The Proxy design pattern is applicable when there is a need to control access to an Object, bem como quando existir a necessidade para uma referência sofisticada para um Objeto. Veremos abaixo situações comuns onde o pattern proxy é aplicável:

* **Virtual Proxies:** atrasa a criação e inicialização de objetos "pesados"/complexos até que sejam necessários. Nesse caso os objetos são criados sob demanda (Por exemplo ao criar um objeto RealSubject somente quando o método doSomething for chamado).
* **Remote Proxies:** proporciona uma representação local para  um objeto que está num espaço de endereçamento diferente.
* **Protection Proxies:** quando o proxy controla o acesso aos métodos do RealSubject, permitindo o acesso para alguns objetos e negando para outros.
* **Smart References:** proporciona um acesso sofisticado para certos objetos como monitoramento do número de referências para um objeto e negação de acesso quando um certo número é atingido, bem como carregar um objeto de banco de dados na memória sob demanda.

Exemplo no mundo real
----------------------------

Um cheque ou cartão de débito é um proxy para fundos (saldo) em uma conta. O cheque ou o cartão de débito pode ser usado no lugar de dinheiro para fazer compras e por fim, controlar o acesso de dinheiro na conta do emitente.

![](http://www.royopa.url.ph/themes/royopa-blog/assets/img/proxy_example1-2x.png)

Nos próximos artigos vou preparar alguns exemplos de códigos para cada tipo de proxy.

Segue abaixo os links usados para fazer esse artigo:

[http://levyfialho.blogspot.com.br/2012/11/proxy-pattern.html](http://levyfialho.blogspot.com.br/2012/11/proxy-pattern.html)

[https://sourcemaking.com/design_patterns/proxy/php](https://sourcemaking.com/design_patterns/proxy/php)

[http://www.oodesign.com/proxy-pattern.html](http://www.oodesign.com/proxy-pattern.html)

[https://sourcemaking.com/design_patterns/proxy](https://sourcemaking.com/design_patterns/proxy)

