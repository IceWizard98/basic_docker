FROM php:8.3-fpm

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    unzip \
    bash \
    libicu-dev \
    libgmp-dev \
    liblz4-dev \
    libmagickwand-dev \
    libxml2-dev \
    libonig-dev \
    libzip-dev \
    libssl-dev \
    zlib1g-dev \
    g++ \
    make \
    autoconf \
    pkg-config \
    && docker-php-ext-install \
        intl \
        pdo_mysql \
        mysqli \
        bcmath \
        gmp \
        pcntl \
        gettext \
        sockets \
        opcache

# Install PECL extensions
RUN pecl install \
    xdebug \
    igbinary \
    redis \
    mongodb \
    imagick \
    && docker-php-ext-enable \
    xdebug \
    igbinary \
    redis \
    mongodb \
    imagick

# Cleanup
RUN apt-get remove --purge -y g++ make autoconf pkg-config \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');"

WORKDIR /var/site
RUN chown www-data:www-data /var/site

EXPOSE 9000
CMD ["php-fpm"]
