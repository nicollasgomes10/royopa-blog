{
"title" : "Integrando SHERPA RoMEO no DSpace",
"author":"Royopa",
"date":"09-07-2015",
"tag":"DSpace, SherpaRomeo",
"slug" : "integrando-sherpa-romeo-no-dspace",
"category":"DSpace"
}

O portal SHERPA/RoMEO (http://www.sherpa.ac.uk/romeo) reúne políticas de 
arquivamento de diversas editoras internacionais. Esta ferramenta permite 
aos usuários e gestores de repositórios consultar as políticas de copyright e 
auto-arquivamento das revistas e editoras sobre o depósito das publicações em 
repositórios de acesso aberto.

Cores do SHERPA/RoMEO
---------------------

O serviço SHERPA/RoMEO utiliza um simples [código de cores](http://www.sherpa.ac.uk/romeo/definitions.php?la=pt&fIDnum=|&mode=simple&version=#colours) para ajudar a consultar as
políticas de arquivamento das editoras. Essas cores foram desenvolvidas a partir 
da lista do projeto RoMEO. Existem quatro categorias de cores, que são descritas
abaixo:

Cores RoMEO | Política de arquivamento
:--- | :---
[Verde][1] | Pode arquivar a versão preprint e postprint ou Versão/PDF do editor
[Azul][2] | Pode arquivar a versão postprint (i.e. o rascunho final após o peer-review) ou Versão/PDF do editor
[Amarelo][3] | Pode arquivar a versão preprint (i.e. antes do peer-review)
[Branco][4] | O arquivo não é suportado formalmente

Integração do SHERPA/RoMEO no DSpace
------------------------------------

O DSpace 5 acrescenta a funcionalidade de lookup da política SHERPA/RoMEO para a 
interface XMLUI. Essa funcionalidade evita o risco de quebrar as restrições de
publicação de forma involuntária. 

Habilitando o lookup SHERPA RoMEO no DSpace adicionará informações nos steps
de submissão. Depois de selecionar um determinado editor ou jornal no formulário
de submissão, o DSpace automaticamente inclui informações sobre permissões de
publicação e restrições ao lado da opção de seleção de arquivos.

Mão na massa!
-------------

Crie sua chave de acesso para a API SHERPA/RoMEO, para ter os benefícios abaixo:

* As aplicações registradas podem exceder o limite de 500 requisições por dia.
* Os usuários registrados recebem avisos antecipados das mudanças planejadas para
a API.
* Estatísticas de uso da API para a sua chave ficam disponíveis mediante solicitação.

Para criar a chave de acesso, use esse link: 
[http://www.sherpa.ac.uk/romeo/apiregistry.php](http://www.sherpa.ac.uk/romeo/apiregistry.php)

Primeiro configure o controle de autoridade do plugin SHERPA/RoMEO no arquivo
[dspace]/config/dspace.cfg conforme abaixo: 

```cfg
#####  SHERPA/Romeo Integration Settings ####
# the SHERPA/RoMEO endpoint
sherpa.romeo.url = http://www.sherpa.ac.uk/romeo/api29.php

# to disable the sherpa/romeo integration
# uncomment the follow line 
# webui.submission.sherparomeo-policy-enabled = false

# please register for a free api access key to get many benefits
# http://www.sherpa.ac.uk/news/romeoapikeys.htm
sherpa.romeo.apikey = YOUR-API-KEY

#####  Authority Control Settings  #####
plugin.named.org.dspace.content.authority.ChoiceAuthority = \
# org.dspace.content.authority.SampleAuthority = Sample, \
# org.dspace.content.authority.LCNameAuthority = LCNameAuthority, \
 org.dspace.content.authority.SHERPARoMEOPublisher = SRPublisher, \
 org.dspace.content.authority.SHERPARoMEOJournalTitle = SRJournalTitle
#  org.dspace.content.authority.SolrAuthority = SolrAuthorAuthority
```
[Veja esse trecho do arquivo aqui](https://github.com/DSpace/DSpace/blob/master/dspace/config/dspace.cfg#L1578-1596)

Altere a seção abaixo do arquivo dspace.cfg, conforme abaixo:

## Demo: publisher name lookup through SHERPA/RoMEO:
choices.plugin.dc.publisher = SRPublisher
choices.presentation.dc.publisher = suggest

## demo: journal title lookup, with ISSN as authority
choices.plugin.dc.title.alternative = SRJournalTitle
choices.presentation.dc.title.alternative = suggest
authority.controlled.dc.title.alternative = true

[Veja esse trecho do arquivo aqui](https://github.com/DSpace/DSpace/blob/master/dspace/config/dspace.cfg#L1666-1673)

E então reinicie o servidor tomcat com o comando abaixo:

```sh
sudo service tomcat7 restart
```

Testando
--------

Para testar a funcionalidade, primeiro pesquisei um registro na [página de busca do SHERPA/RoMEO][5]
com o ISSN 1210-8510, que é um Jornal com a cor branca, conforme mostrado na 
imagem abaixo:

![](http://www.royopa.url.ph/themes/royopa-blog/assets/img/sherpa-romeo-consulta-registro-issn.jpg)

E então inclui um novo item no DSpace e usei o ISSN 1210-8510, como mostrado na
figura abaixo:

![](http://www.royopa.url.ph/themes/royopa-blog/assets/img/sherpa-romeo-upload-inclusao-de-issn.jpg)

E então quando eu chego até a página do formulário para fazer o upload do arquivo,
veja que as informações do registro no SHERPA/RoMEO (Publisher Information) são
exibidas:

![](http://www.royopa.url.ph/themes/royopa-blog/assets/img/sherpa-romeo-upload-arquivos.jpg)


Links
-----

https://wiki.duraspace.org/display/DSPACE/Authority+Control+of+Metadata+Values

http://www.sherpa.ac.uk/romeo/

https://sites.google.com/site/projectoblimunda/exemplos-internacionais/sherpa-romeo


[1]:http://www.sherpa.ac.uk/romeo/browse.php?colour=green&la=pt&fIDnum=|&mode=simple
[2]:http://www.sherpa.ac.uk/romeo/browse.php?colour=blue&la=pt&fIDnum=|&mode=simple
[3]:http://www.sherpa.ac.uk/romeo/browse.php?colour=yellow&la=pt&fIDnum=|&mode=simple
[4]:http://www.sherpa.ac.uk/romeo/browse.php?colour=white&la=pt&fIDnum=|&mode=simple
[5]:http://www.sherpa.ac.uk/romeo/search.php
