FROM php:7.4-fpm

# Get user id and group id from arguments.
ARG UID
ARG GID

# Add user.
RUN sed -i -e"s/^user\s=\swww-data/user = serveruser/" /usr/local/etc/php-fpm.d/www.conf && \
  sed -i -e"s/^group\s=\swww-data/group = serveruser/" /usr/local/etc/php-fpm.d/www.conf && \
  sed -i -e"s/^;php_admin_value\[memory_limit\]\s=\s32M/php_admin_value[memory_limit] = 256M\nphp_admin_value[max_input_vars] = 5000/" /usr/local/etc/php-fpm.d/www.conf && \
  echo "\ncatch_workers_output = yes\nphp_admin_value[error_log] = /var/log/fpm-php.www.log\nphp_admin_flag[log_errors] = on\n" >> /usr/local/etc/php-fpm.d/www.conf && \
  touch /usr/local/etc/php/php.ini && echo "memory_limit = 256M\n" >> /usr/local/etc/php/php.ini && \
  touch /var/log/fpm-php.www.log && chmod 777 /var/log/fpm-php.www.log && \
  groupadd -r serveruser -g $GID && useradd -ms /bin/bash serveruser -u $UID -g $GID

RUN docker-php-ext-install pdo_mysql

# GD.
RUN apt-get update && \
  apt-get install -y \
  libpng-dev libjpeg-dev git wget nano netcat zip unzip mysql-client

RUN docker-php-ext-configure gd \
  --with-jpeg-dir=/usr/include/

RUN docker-php-ext-install gd

# Opcache.
RUN docker-php-ext-configure opcache --enable-opcache && \
  docker-php-ext-install opcache

# Xdebug.
RUN pecl install xdebug-2.6.0 && docker-php-ext-enable xdebug

# Install composer.
RUN cd ~
RUN curl -sS https://getcomposer.org/installer -o composer-setup.php
RUN php composer-setup.php --install-dir=/usr/local/bin --filename=composer
RUN rm -rf composer-setup.php

USER serveruser

# Install Code sniffer.
USER serveruser
RUN composer global require drupal/coder dealerdirect/phpcodesniffer-composer-installer friendsofphp/php-cs-fixer squizlabs/php_codesniffer && \
  /home/serveruser/.composer/vendor/bin/phpcs --config-set installed_paths ~/.composer/vendor/drupal/coder/coder_sniffer

# Instal drush.
RUN mkdir /home/serveruser/.drush && cd /home/serveruser/.drush && \
  composer require drush/drush && echo "PATH=$PATH:~/.composer/vendor/bin" >> ~/.bashrc
USER root
RUN chmod u+x /home/serveruser/.drush/vendor/bin/drush && \
  ln -s /home/serveruser/.drush/vendor/bin/drush /usr/bin/drush

# Drupal console installation.
RUN curl https://drupalconsole.com/installer -L -o drupal.phar && \
  mv drupal.phar /usr/local/bin/drupal && \
  chmod +x /usr/local/bin/drupal

# Install phpunit.
RUN wget https://phar.phpunit.de/phpunit.phar && \
  chmod +x phpunit.phar && \
  mv phpunit.phar /usr/local/bin/phpunit

# Install file.
COPY install_backend.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/install_backend.sh

USER serveruser

WORKDIR /home/serveruser/www/backend
ENTRYPOINT ["php-fpm"]