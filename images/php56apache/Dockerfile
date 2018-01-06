FROM php:5.6-apache

RUN apt-get update && apt-get install -y --force-yes \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
   && a2enmod vhost_alias \
   && a2enmod rewrite \
   && docker-php-ext-install -j$(nproc) mysqli pdo pdo_mysql \
   && docker-php-ext-enable mysqli pdo pdo_mysql \
   && docker-php-ext-install -j$(nproc) iconv mcrypt \
   && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
   && docker-php-ext-install -j$(nproc) gd