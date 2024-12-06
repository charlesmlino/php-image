FROM php:8.1.3-apache

WORKDIR /home/webmaster

RUN apt-get update && apt-get install -y \
        libpng-dev \
        zlib1g-dev \
        libxml2-dev \
        libzip-dev \
        libpq-dev \
        libonig-dev \
        zip \
        curl \
        unzip \
        unixodbc \
        unixodbc-dev \
        freetds-dev \
        freetds-bin \
        libicu-dev \
        logrotate \
    && ln -s /usr/lib/x86_64-linux-gnu/libsybdb.a /usr/lib/ \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-install pdo_dblib \
    && docker-php-ext-configure gd \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install pdo \
    && docker-php-ext-install pdo_pgsql \
    && docker-php-ext-install pgsql \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install sockets \
    && docker-php-ext-install zip \
    && docker-php-ext-install xml \
    && docker-php-ext-install opcache \
    && docker-php-ext-install soap \
    && docker-php-source delete

RUN pecl install xdebug \
    && docker-php-ext-enable xdebug
    
# APACHE-PHP CONFIG
COPY ./vhost.conf /etc/apache2/sites-available/000-default.conf
COPY ./apache.conf /etc/apache2/conf-enabled/zzzcustom.conf
COPY ./php.ini /usr/local/etc/php/conf.d/zzzcustom.ini

RUN chmod 755 -R /var/log/apache2
RUN chown -R www-data:www-data /home/webmaster \
    && a2enmod rewrite
ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# SSL CONFIG
run sed -i '1issl_conf = ssl_sect' /etc/ssl/openssl.cnf
run sed -i '1i[ default_conf ]' /etc/ssl/openssl.cnf
run sed -i '1iopenssl_conf = default_conf' /etc/ssl/openssl.cnf

# COMPOSER
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN apt-get update && apt-get install -y inetutils-ping netcat
    

RUN groupadd -g 999 webmaster && \
    useradd -r -u 999 -g webmaster webmaster
USER webmaster