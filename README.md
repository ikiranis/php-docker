# Docker for PHP

This is a development environment for PHP.
It contains,
- PHP-FPM
- GD
- Composer
- Xdebug

# Installation
Requirements
- You need to have [Docker](https://docs.docker.com/engine/installation/) installed

Run in root folder,
~~~~
docker-compose build && docker-compose up -d
~~~~

Login to the container,
~~~~
docker exec -it app_fpm /bin/bash
~~~~

or in PHPstorm. In docker services choose app_fpm container. Right click and click exec.
Add

```
/bin/bash
```


Project files will be in data/www

Create Laravel project
~~~~
cd www
rm index.php
composer create-project --prefer-dist laravel/laravel .
~~~~

Serve
~~~~
php artisan serve --host 0.0.0.0
~~~~

**Setup PHPStorm for PHPUnit**
<https://blog.jetbrains.com/phpstorm/2016/11/docker-remote-interpreters/>

First you must build and run the container

We can add a new interpreter from the preferences pane, by selecting Languages & Frameworks, then PHP, and clicking the […] button next to the interpreter drop down. Next, we click the [+] button to add a new interpreter and select Remote.
