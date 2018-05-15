FROM php:7.2.2-apache

RUN apt-get update \
    && apt-get install -y \
        # Install mcrypt extension
        libmcrypt-dev \
        libssl-dev \
        \
        # Install gd and iconv extension
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        \
        # Install intl extension
        g++ \
        libicu-dev \
        \
        # Install xml extension
        libxml2-dev \
        libxml++2.6-dev \
        \
        # Install postgresql extension
        libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Install mcrypt and APCU
RUN pecl install mcrypt-1.0.1 \
    && pecl install APCu-5.1.10 \
    && pecl install apcu_bc-1.0.4

# Configure Apache
RUN a2enmod rewrite

# Copy php config file to the container
COPY php.ini-development /usr/local/etc/php/php.ini

# Configure PHP
RUN docker-php-ext-enable mcrypt
# RUN docker-php-ext-enable apcu
# RUN docker-php-ext-enable apc
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
RUN docker-php-ext-install -j$(nproc) iconv gd
RUN docker-php-ext-install intl
RUN docker-php-ext-install zip
RUN docker-php-ext-install mbstring
RUN docker-php-ext-install xml
RUN docker-php-ext-configure pgsql --with-pgsql=/usr/local/pgsql
RUN docker-php-ext-install pdo pdo_pgsql pgsql
RUN echo "extension=apcu.so" | tee -a /usr/local/etc/php/conf.d/20-apcu.ini
RUN echo "extension=apc.so" | tee -a /usr/local/etc/php/conf.d/21-apc.ini

# Install mongodb extension
RUN pecl install mongodb
RUN echo "extension=mongodb.so" | tee -a /usr/local/etc/php/conf.d/ext-mongodb.ini

# Gives the Apache user access rights to the default html directory
RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 777 /var/www/html

# Installs Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Sets the workdir
WORKDIR /var/www/html/app

# Aliases the Symfony command
RUN alias sf="php bin/console"

# Exposes bash as the default command
# CMD [ "bash" ]
