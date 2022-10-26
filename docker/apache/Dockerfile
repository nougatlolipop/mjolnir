FROM php:7.4-apache
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf
RUN apt-get -y update
RUN apt-get -y upgrade
RUN apt-get install -y\
    git \
    zip \
    curl \
    sudo \
    unzip \
    libicu-dev \
    libbz2-dev \
    libpng-dev \
    libjpeg-dev \
    libmcrypt-dev \
    libreadline-dev \
    libfreetype6-dev \
    g++ \
    vim

RUN docker-php-ext-install \
    bz2 \
    intl \
    bcmath \
    opcache \
    calendar \
    pdo_mysql \
    mysqli

# 2. setingan document root untuk apache
COPY docker/apache/000-default.conf /etc/apache2/sites-available/000-default.conf

# 3. mod_rewrite untuk URL rewrite and mod_headers untuk .htaccess extra headers --> Access-Control-Allow-Origin-
RUN a2enmod rewrite headers

# 4. start with base php config, kemudian add extensions
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

# 5. install composer
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer
RUN chmod +x /usr/local/bin/composer
RUN composer self-update

# COPY src/ /var/www/html/
# 6. butuh pengguna yang sama dengan UID/GID dengan host user
ARG uid
RUN useradd -G www-data,root -u $uid -d /home/devuser devuser
RUN mkdir -p /home/devuser/.composer && \
    chown -R devuser:devuser /home/devuser

# open permission 
RUN chmod 777 ./ -R

# create codeigniter 4
RUN composer create-project codeigniter4/appstarter ./

VOLUME ./app:/var/www/html

EXPOSE 80