# Royopa Blog

[![Build Status](https://travis-ci.org/royopa/royopa-blog.svg?branch=master)](https://travis-ci.org/royopa/royopa-blog)

http://royopa.url.ph

FTP host      ftp.royopa.url.ph

FTP IP        185.28.21.44

FTP Port      21

FTP username  u177069854

FTP password  ••••••••••

Folder        public_html

This is my blog using [TextPress](http://textpress.shameerc.com).

## Install & Build

Make sure you have Composer installed, run `composer install`

## Execute

To see a real-live page in action, start the PHP built-in web server with
command:

```sh
composer run
```

Then, browse to [http://localhost:8000/](http://localhost:8000/)

## Deploy

```sh
php ./vendor/royopa/phploy/bin/phploy.phar --server production
```
or

```sh
composer deploy
```

## Assets images folder

Add the images in articles here ./themes/royopa-blog/assets/img/
and use the full address in MD, example: ![](http://www.royopa.url.ph/themes/royopa-blog/assets/img/embargo-simples.png) 
