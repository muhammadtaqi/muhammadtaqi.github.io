---
layout: post
title: "Composer Setup and It's working"
date: 2016-12-20T19:37:22+05:00
comments: true
categories: PHP
---

# Composer Setup and It's working

> Composer is a dependency manager for Your Projects. It enables you to declare the libraries that your projects depend on and Install or Update them any time in just single command.

## How it Works:

In short;

> Composer is Just an Abstract Implementation of PHP's `spl_autoload_register` of SPL Library.

Before PHP 5 , We use PHP's [include][1] and [require][2] functions to load our dependent classes or functions into the PHP file. Normally We create a directory called 'classes' that holds all of classes.

A very nice feature in PHP 5 is **autoloading** which allows you to register multiple .php class files that PHP will put into a stack/queue and to be automatically loaded when a "new Class" is needed/declared. So, only those files that need to be included are included (a little bit of memory savings). This is also called **Load on Demand**.

If we scroll down your vendor/composer directory and see you will see all the interfaces and classes composer are just implementation of PHP SPL Library. The `vendor/autoload.php` file is basically a fake loader, inside it calls `vendor/composer/autoload_real.php`.

## Installation and Usage:

Ultimately as a user, all you have to do is to create a `composer.json` file in your project root. This `composer.json` file defines your project dependencies, and optionally configures composer.

So let's get to the terminal directory project we established our directory.

```bash
cd /var/www/html/symfony3-sandbox
```

Composer can be installed with curl or php.

With curl:

```bash
curl -sS https://getcomposer.org/installer | php
```


```bash
php -r "readfile('https://getcomposer.org/installer');" | php
touch composer.json
```


Open composer.json file and put below in the file.
``` json
{
        "require": {
            "symfony/validator":         "2.1.*",
            "doctrine/dbal":             "2.2.*",
            "monolog/monolog":           "dev-master",
            "jtreminio/test-extensions": "dev-master"
        },
        "minimum-stability": "dev"
}
```

then run
```bash
$ php composer.phar install
```

Now you will have `composer.json,` `composer.lock` and `composer.phar` file as well as a `vendor` directory has been created which should contain all dependencies – including composer itself.

## Packagist:

Every packages you can use with Composer are stored in [Packagist][3] site. There you can browse packages and submit yours too.

[1]: http://php.net/manual/en/function.include.php
[2]: http://php.net/manual/en/function.require.php
[3]: https://packagist.org/
