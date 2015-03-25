{
"title" : "Criando um controle de autoridades customizado no DSpace",
"author":"Royopa",
"date":"25-03-2015",
"tag":"dspace",
"slug" : "criando-um-controle-de-autoridades-customizado-no-dspace",
"category":"DSpace"
}

https://github.com/royopa/dspace-tematres

Copiar a classe TematresProtocol.java no diretório [dspace-src]dspace-api/src/main/java/org/dspace/content/authority/.

Recompilar o DSpace



## Incluir a configuração no dspace.cfg para usar o controle de autoridades
org.dspace.content.authority.TematresSponsorship = TematresSponsorship

## URL para acesso ao web service do Tematres
tematres.url = http://bdpife2.sibi.usp.br/a/tematres/vocab/services.php

## Configurado plugin para Sponsorship para acesso aos dados do Tematres
choices.plugin.dc.description.sponsorship = TematresSponsorship
choices.presentation.dc.description.sponsorship = lookup
authority.controlled.dc.description.sponsorship = true


