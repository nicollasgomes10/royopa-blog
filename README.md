# Royopa Blog

[![Build Status](https://travis-ci.org/royopa/royopa-blog.svg?branch=master)](https://travis-ci.org/royopa/royopa-blog)

www.royopa.url.ph

This is my blog using [TextPress](http://textpress.shameerc.com).

## Install & Build

Make sure you have Composer installed, run `composer install`

## Execute

To see a real-live page in action, start the PHP built-in web server with
command:

    $ composer run

Then, browse to http://localhost:8000/

## Deploy

    $ php ./vendor/royopa/phploy/bin/phploy.phar --server production

    or

    $ composer deploy
