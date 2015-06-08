{
"title" : "Sincronizando as configurações do Sublime no Dropbox",
"author":"Royopa",
"date":"07-06-2015",
"tag":"coding, dropbox, plugins, preferences, python, settings, sublime text 3, sync",
"slug" : "sincronizando-as-configuracoes-do-sublime-no-dropbox",
"category":"sublime"
}

Tradução do artigo [Sync Sublime Text 3 settings with Dropbox](http://www.alexconrad.org/2013/07/sync-sublime-text-3-settings-with.html) do [Alex Conrad](https://twitter.com/alexconrad).

I recently upgraded to Sublime Text 3 (ST3) and thought this was a good time for me to figure out how to synchronize my ST3 preferences between my work and home computers. Especially since ST3 uses Python 3: not all plugins available with Sublime Package Control work in Python 3 yet, so I often have to do manual installs, which sucks if you have to do it on all of your computers running Sublime Text 3.

Algumas das minhas configurações
--------------------------------

Turns out, synchronizing ST3 is actually very simple if you use any kind of file hosting and synchronization service, such as Dropbox. 

There are discussions out there on how to do it with Sublime Text 2, but not so much with version 3. That being said, the steps are almost identical and you would certainly figure it out yourself. But hey, you won't even have to!

Sincronizando o Sublime
-----------------------

The steps below should to be done on the computer that has your most up-to-date ST3 settings (the ones you want to propagate to other computers).

Before doing anything, close Sublime Text 3, just to be clean.

# Create the sync directory in Dropbox
$ mkdir ~/Dropbox/sublime-text-3/

# Move your ST3 "Packages" and "Installed Packages" to Dropbox
$ cd ~/.config/sublime-text-3/
$ mv Packages/ ~/Dropbox/sublime-text-3/
$ mv Installed\ Packages/ ~/Dropbox/sublime-text-3/

# Then symlink your Dropbox directories back locally
$ ln -s ~/Dropbox/sublime-text-3/Packages/
$ ln -s ~/Dropbox/sublime-text-3/Installed\ Packages/

Note that ST3 has three other directories under ~/.config.sublime-text-3/: Cache, Index and Local. Do not sync these, each computer should manage their own local copies.

Sincronizando outros computadores
---------------------------------

All other computers will now have ~/Dropbox/sublime-text-3/. Follow these steps on each of the computers you have ST3 installed to sync them:

# Remove the "outdated" directories
$ cd ~/.config/sublime-text-3/
$ rm -rf Packages/
$ rm -rf Installed\ Packages/

# Then symlink your Dropbox directories back locally
$ ln -s ~/Dropbox/sublime-text-3/Packages/
$ ln -s ~/Dropbox/sublime-text-3/Installed\ Packages/

Now when you tweak to your Sublime Text 3 settings, they will propagate automatically. Even new packages get installed everywhere! You'll always feel at home, wherever you are!
