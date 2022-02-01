FROM php:8.1.1-fpm

COPY php.ini /usr/local/etc/php/conf.d/php-fpm.ini
COPY index.php /var/www/public/index.php

RUN apt-get update

# Install GD with various filetypes support
RUN apt-get install -y \
    libfreetype6-dev libpng-dev libjpeg62-turbo-dev libwebp-dev
RUN docker-php-ext-configure gd \
        --enable-gd \
        --with-freetype=/usr/include/ \
        --with-jpeg=/usr/include/ \
        --with-webp=/usr/include/ \
    && NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    && docker-php-ext-install -j${NPROC} gd
RUN docker-php-ext-install exif

# zip - required for composer
RUN apt-get install -y libzip-dev zip \
    && docker-php-ext-configure zip \
    && docker-php-ext-install zip

# install other required extensions
RUN docker-php-ext-install bcmath
RUN docker-php-ext-install pdo_mysql

# install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

WORKDIR /var/www

COPY xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

## Install Xdebug extension + cleanup
RUN pecl install xdebug
RUN docker-php-ext-enable xdebug

RUN useradd -r -u 1000 elywejnak
USER elywejnak