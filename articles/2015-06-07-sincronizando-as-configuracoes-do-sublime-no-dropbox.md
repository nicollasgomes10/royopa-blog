{
"title" : "Sincronizando as configurações do Sublime no Dropbox",
"author":"Royopa",
"date":"07-06-2015",
"tag":"coding, dropbox, plugins, preferences, python, settings, sublime text 3, sync",
"slug" : "sincronizando-as-configuracoes-do-sublime-no-dropbox",
"category":"sublime"
}

Hoje eu reinstalei o meu xUbuntu do notebook e sei que dá o maior trabalho para reinstalar o Sublime Text com todas as configurações e pacotes que eu tinha. Diante disso estava pesquisando uma maneira fácil de manter essas configurações sincronizadas para todas as máquinas que utilizo. 

Espero que seja útil! Tradução do artigo [Sync Sublime Text 3 settings with Dropbox](http://www.alexconrad.org/2013/07/sync-sublime-text-3-settings-with.html) do [Alex Conrad](https://twitter.com/alexconrad).

Recentemente eu atualizei para o Subliem Text 3 (ST3) 

I recently upgraded to Sublime Text 3 (ST3) e achei que era uma boa hora para descobrir como sincronizar as minhas preferências do ST3 entre os computadores do meu trabalho e de casa. Especialmente por que o ST3 usa o Python 3: nem todos os plugins disponíveis no Sublime Package Control funcionam no Python 3, então frequentemente eu tenho que fazer a instalação manual, o que é muito chato se você tem que fazer em todos os outros computadores que rodam o Sublime Text 3.

Algumas das minhas configurações
--------------------------------

Acontece que, a sincronização do ST3 é muito simples se você usar um qualquer tipo de hospedagem de arquivos e serviços de sincronização como o Dropbox.

Existem discussões de como fazer isso no Sublime Text 2 mas não muito com a versão 3. Dito isto, os passos são quase idênticos e você certamente descobriria sozinho. Mas hey, você não terá que fazer isso!

Sincronizando o Sublime
-----------------------

Os passos a seguir devem ser feitas no computador que tem as suas configurações ST3 mais atualizadas (aquelas que você deseja propagar para outros computadores).

Antes de fazer qualquer coisa, feche o Sublime Text 3.

```shell
# Create the sync directory in Dropbox
$ mkdir ~/Dropbox/sublime-text-3/

# Move your ST3 "Packages" and "Installed Packages" to Dropbox
$ cd ~/.config/sublime-text-3/
$ mv Packages/ ~/Dropbox/sublime-text-3/
$ mv Installed\ Packages/ ~/Dropbox/sublime-text-3/

# Then symlink your Dropbox directories back locally
$ ln -s ~/Dropbox/sublime-text-3/Packages/
$ ln -s ~/Dropbox/sublime-text-3/Installed\ Packages/
```

Note that ST3 has three other directories under ~/.config.sublime-text-3/: Cache, Index and Local. Do not sync these, each computer should manage their own local copies.

Sincronizando outros computadores
---------------------------------

All other computers will now have ~/Dropbox/sublime-text-3/. Follow these steps on each of the computers you have ST3 installed to sync them:

```shell
# Remove the "outdated" directories
$ cd ~/.config/sublime-text-3/
$ rm -rf Packages/
$ rm -rf Installed\ Packages/

# Then symlink your Dropbox directories back locally
$ ln -s ~/Dropbox/sublime-text-3/Packages/
$ ln -s ~/Dropbox/sublime-text-3/Installed\ Packages/
```

Now when you tweak to your Sublime Text 3 settings, they will propagate automatically. Even new packages get installed everywhere! You'll always feel at home, wherever you are!
