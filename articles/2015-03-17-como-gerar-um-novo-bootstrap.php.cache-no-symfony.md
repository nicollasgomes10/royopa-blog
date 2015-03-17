{
"title":"Como gerar um novo 'bootstrap.php.cache' no Symfony",
"author":"Royopa",
"date":"17-03-2015",
"tag":"php",
"slug":"como-gerar-um-novo-bootstrap-php-cache-no-symfony",
"category":"PHP"
}

Como é muito comum termos diversas dependências/aplicações de terceiros em uma aplicação, o Symfony possui um mecanismo para tentar diminuir a utilização do disco ao ter que carregar cada uma das classes em arquivos separados.

O Symfony cria um arquivo de inicialização que contém múltiplas classes em um único arquivo chamado de ***bootstrap.php.cache***. 

O arquivo ***bootstrap.php.cache*** é recriado toda vez que você utilizar o comando ***composer install***.

Poŕem quando você utiliza o comando ***composer update*** o arquivo ***bootstrap.php.cache*** não é regerado. Veja abaixo como gerar um novo arquivo bootstrap.php.cache.

## Gerando um novo "bootstrap.php.cache" no Symfony +2.1

Para gerar um novo arquivo bootstrap.php.cache no Symfony2 (versão +2.1), basta executar o seguinte comando:

    $ cd [your_app_dir]
    $ php ./vendor/sensio/distribution-bundle/Sensio/Bundle/DistributionBundle/Resources/bin/build_bootstrap.php

Mais informações sobre o arquivo de cache podem ser encontradas na [Documentação Oficial do Symfony][1]

[1]: (http://symfony.com/doc/current/book/performance.html#use-bootstrap-files)
