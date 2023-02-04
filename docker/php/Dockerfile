FROM php:8.2-fpm

USER root

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libpq-dev \
    libzip-dev \
    libtidy-dev \
    libcurl4-openssl-dev \
    libonig-dev \
    libmagickwand-dev \
    libxml2-dev \
    zip \
    unzip

# Install PHP extensions
RUN docker-php-ext-install \
    curl \
    pdo \
    pdo_mysql \
    pdo_pgsql\
    pgsql \
    mbstring \
    zip \
    mysqli \
    xml \
    tidy \
    exif \
    intl \
    pcntl \
    bcmath \
    sockets

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

ARG XDEBUG=false

ARG UNAME=www-data
ARG UGROUP=www-data
ARG UID=1000
ARG GID=1000
RUN usermod  --uid $UID $UNAME
RUN groupmod --gid $GID $UGROUP

RUN if "$XDEBUG" == "true" ; then yes | pecl channel-update pecl.php.net && pecl install xdebug-3.1.4 \
      && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
      && echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/xdebug.ini \
      && echo "xdebug.start_with_request=yes" >> /usr/local/etc/php/conf.d/xdebug.ini  \
      && echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/xdebug.ini  \
      && echo "xdebug.client_port=9003" >> /usr/local/etc/php/conf.d/xdebug.ini  \
      && echo "xdebug.idekey=PHPSTORM" >> /usr/local/etc/php/conf.d/xdebug.ini  \
    ; else yes | echo XDebug is not need to install ; fi

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

RUN echo "upload_max_filesize=2G" >> /usr/local/etc/php/conf.d/custom.ini
RUN echo "post_max_size=2G" >> /usr/local/etc/php/conf.d/custom.ini

COPY ./src /var/www
COPY ./src/.env.example /var/www/.env

# Set working directory
WORKDIR /var/www

RUN chown -R "$UNAME:$UGROUP" /var/www

USER $UNAME

RUN composer install

