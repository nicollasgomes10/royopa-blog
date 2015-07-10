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

RoMEO colours
-------------

O servico SHERPA/RoMEO utiliza um simples [código de cores](http://www.sherpa.ac.uk/romeo/definitions.php?la=pt&fIDnum=|&mode=simple&version=#colours) para ajudar a consultar as
políticas de arquivamento das editoras. Essas cores foram desenvolvidas a partir 
da lista do projeto RoMEO. Existem quatro categorias de cores, que são descritas
abaixo:

Cores RoMEO | Política de arquivamento
:---: | :---:
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

Primeiro configure o controle de autoridade do plugin SHERPA/RoMEO no arquivo
[dspace]/config/dspace.cfg conforme abaixo: 

```cfg
sherpa.romeo.url = http://www.sherpa.ac.uk/romeo/api29.php
```
[Veja esse trecho do arquivo aqui](https://github.com/DSpace/DSpace/blob/master/dspace/config/dspace.cfg#L1580)

Altere a seção "Authority Control Settings" do arquivo dspace.cfg, conforme abaixo:

#####  Authority Control Settings  #####
plugin.named.org.dspace.content.authority.ChoiceAuthority = \
 org.dspace.content.authority.SHERPARoMEOPublisher = SRPublisher, \
 org.dspace.content.authority.SHERPARoMEOJournalTitle = SRJournalTitle

[Veja esse trecho do arquivo aqui](https://github.com/DSpace/DSpace/blob/master/dspace/config/dspace.cfg#L1590-1596)



Links
-----

https://wiki.duraspace.org/display/DSPACE/Authority+Control+of+Metadata+Values

http://www.sherpa.ac.uk/romeo/

https://sites.google.com/site/projectoblimunda/exemplos-internacionais/sherpa-romeo


[1]: (http://www.sherpa.ac.uk/romeo/browse.php?colour=green&la=pt&fIDnum=|&mode=simple)
[2]: (http://www.sherpa.ac.uk/romeo/browse.php?colour=blue&la=pt&fIDnum=|&mode=simple)
[3]: (http://www.sherpa.ac.uk/romeo/browse.php?colour=yellow&la=pt&fIDnum=|&mode=simple)
[4]: (http://www.sherpa.ac.uk/romeo/browse.php?colour=white&la=pt&fIDnum=|&mode=simple)
