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
cp .env.example .env
docker-compose build && docker-compose up -d
~~~~

Login to the container,
~~~~
docker exec -it app_fpm /bin/bash -c "TERM=$TERM exec bash"
~~~~

Create Laravel project
----
composer create-project --prefer-dist laravel/laravel .
----

