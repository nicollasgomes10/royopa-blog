{
    "title" : "Instalando o Wildfly no Ubuntu",
    "author":"Royopa",
    "date":"03-02-2016",
    "tag":"design pattern, proxy, virtual proxy",
    "slug" : "instalando-o-wildfly-no-ubuntu",
    "category":"Java"
}

Wildfly 10 é a última versão do servidor anteriormente conhecido como Jboss Application Server (Jboss AS). Nesse artigo nós veremos como fazer o download, instalar e configurar o Wildfly no Linux Ubuntu.
O artigo original pode ser encontrado em [http://alibassam.com/how-to-install-wildfly-9-on-debian-based-linux-distribution/](http://alibassam.com/how-to-install-wildfly-9-on-debian-based-linux-distribution/)

![](http://www.royopa.url.ph/themes/royopa-blog/assets/img/wildfly.png) 

Requisitos
------------

Para a instação da versão 10 do Wildfire você deve ter pelo menos o [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) instalado.

Download
-----------

Primeiro, precisamos obter o link de download, simplesmente acesse a página [http://wildfly.org/downloads/](http://wildfly.org/downloads/) e você verá a lista das versões disponíveis que podem ser baixadas, nós escolheremos a última disponível (10.0.0.Final), então copie o link de donwload do arquivo ZIP:

![](http://www.royopa.url.ph/themes/royopa-blog/assets/img/download-wildfly.png)

Agora que você tem o link e assumindo que você está no diretório downloads do usuário, nós devemos executar os seguintes comandos para baixar o Wildfly:

```sh
cd /home/rodrigo/downloads
wget http://download.jboss.org/wildfly/10.0.0.Final/wildfly-10.0.0.Final.zip
```

O console informará o progresso do download e quando tiver terminado nós temos um novo arquivo zip na nossa pasta de downloads: wildfly-10.0.0.Final.zip

Instalação
------------

O processo de instalação do Wildfly é muito simples, primeiro nós precisamos descompactar o arquivo:

```sh
$ unzip wildfly-10.0.0.Final.zip
```

Agora nós temos um novo diretório na sua pasta downloads, chamada wildfly-10.0.0.Final.

Nós precisamos somente colocá-lo em alguma pasta, você pode colocar onde você quiser, talvez no seu diretório home, mas é preferível colocá-lo em /opt (com permissão de sudo). Você pode executar os seguintes comandos para colocar o novo diretório num local mais conveniente para você:

```sh
mv wildfly-10.0.1.Final /home/rodrigo/wildfly
```

No caso você pode colocar a pasta em /opt:

```sh
sudo -i
[Provide your password......]
mv wildfly-10.0.0.Final /opt/wildfly
```

O **diretório** onde o Wildfly foi instalado e o usuário que controla esta instalação e o diretório são dois parâmetros importantes que nós usaremos depois.

Configuração
---------------

Antes de rodar o Wildfly, algumas configurações devem ser feitas.

##Porta

A porta padrão para o Wildfly é a 8080, mas talvez você tenha instaldo o Tomcat antes então você precisa executá-lo em uma porta diferente. Para mudar a porta você precisa editar o arquivo standalone.xml localizado em:

```
[diretorio_de_instalacao_wildfly]/standalone/configuration/standalone.xml
```

Localize o trecho abaixo:

```xml
<socket-binding-group name="standard-sockets" default-interface="public" port-offset="${jboss.socket.binding.port-offset:0}">
```

E mude o offset para o valor desejado, por exemplo, se eu mudar o offset para 10:

```
<socket-binding-group name="standard-sockets" default-interface="public" port-offset="${jboss.socket.binding.port-offset:10}">
```

Então todas as portas vão mudar e aumentarão 10 unidades, a porta padrão HTTP será 8090 em vez de 8080.

##Gerenciamento de usuários

Você precisa do gerenciamento de usuários se você quiser usar a interface web de gerenciamento. Para cadstrar um novo usuário você precisa executar o seguinte script:

```sh
[diretorio_de_instalacao_wildfly]/bin/add-user.sh
```

O sistema vai te pedir um usuário e um password e então o usuário será criado.

##Acesso Web

Por padrão você poderá acessar o Wildfly somente localmente, pelo endereço [http://localhost:8080](http://localhost:8080) (ou qualquer outra porta que você configurou anteriormente)

Se você precisa habilitar acesse externos, você deve localizar o seguinte trecho no arquivo standalone.xml:

```xml
<interface name="management">
    <inet-address value="${jboss.bind.address.management:127.0.0.1}"/>
</interface>
<interface name="public">
    <inet-address value="${jboss.bind.address:127.0.0.1}"/>
</interface>
```

E mude para:

```xml
<interface name="management">
    <any-address/>
</interface>
<interface name="public">
    <any-address/>
</interface>
```

Rodando
----------

Para rodar o Wildfly execute o script standalone.sh localizado no diretório:

```cmd
[diretorio_de_instalacao_wildfly]/bin/standalone.sh
```

Essa é uma abordagem simples e não requer nenhuma configuração.

Se tudo estiver ok, você poderá acessar a aplicação pelo endereço [http://localhost:8080](http://localhost:8080) (ou qualquer outra porta que você configurou anteriormente)

![](http://www.royopa.url.ph/themes/royopa-blog/assets/img/wildfly-tela-inicial.png) 
