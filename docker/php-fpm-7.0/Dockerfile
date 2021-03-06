FROM php:7.0-fpm

# $ docker run -it --rm php:7.0-fpm docker-php-ext-install
# Possible values for ext-name:
# bcmath bz2 calendar ctype curl dba dom enchant exif fileinfo filter ftp gd gettext gmp hash iconv imap interbase intl json ldap mbstring mcrypt mysqli oci8 odbc opcache pcntl pdo pdo_dblib pdo_firebird pdo_mysql pdo_oci pdo_odbc pdo_pgsql pdo_sqlite pgsql phar posix pspell readline recode reflection session shmop simplexml snmp soap sockets spl standard sysvmsg sysvsem sysvshm tidy tokenizer wddx xml xmlreader xmlrpc xmlwriter xsl zip
# Some of the above modules are already compiled into PHP; please check
# the output of "php -i" to see which modules are already loaded.
RUN apt-get update && \
    apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libmcrypt-dev \
        libicu-dev \
        libxslt-dev \
        cron \
        curl \
        vim \
        unzip \
        build-essential \
        git \
    && docker-php-ext-install -j$(nproc) mcrypt \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) zip \
    && docker-php-ext-install -j$(nproc) intl \
    && docker-php-ext-install -j$(nproc) xsl \
    && docker-php-ext-install -j$(nproc) pdo_mysql \
    # mysqli is needed for phpMyAdmin
    && docker-php-ext-install -j$(nproc) mysqli \
    && docker-php-ext-install -j$(nproc) soap \
    && docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-install -j$(nproc) opcache

RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

COPY conf/php.ini /usr/local/etc/php/php.ini
COPY conf/php-cli.ini /usr/local/etc/php/php-cli.ini

# Enable and configure xdebug
RUN pecl install xdebug
RUN docker-php-ext-enable xdebug

# https://intellij-support.jetbrains.com/hc/en-us/community/posts/115000507430--SOLVED-How-to-setup-phpStorm-xDebug-Docker-in-Windows-
# https://xdebug.org/docs/remote
RUN sed -i '1 a xdebug.remote_enable=1' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN sed -i '1 a xdebug.remote_connect_back=0' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN sed -i '1 a xdebug.remote_autostart=0' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN sed -i '1 a xdebug.remote_mode=req' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN sed -i '1 a xdebug.remote_host=host.docker.internal' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN sed -i '1 a xdebug.remote_port=10000' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN sed -i '1 a xdebug.remote_timeout=1000' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN sed -i '1 a xdebug.max_nesting_level=1000' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN sed -i '1 a xdebug.remote_log=""' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini




### POST-BUILD commands
# https://www.phpmyadmin.net/downloads/
### RUN curl https://files.phpmyadmin.net/phpMyAdmin/4.8.3/phpMyAdmin-4.8.3-english.tar.xz --output /tmp/phpMyAdmin-4.8.3-english.tar.xz && \
###     tar -xf /tmp/phpMyAdmin-4.8.3-english.tar.xz --directory /var/www/html && \
###     ln -s /var/www/html/phpMyAdmin-4.8.3-english /var/www/html/phpmyadmin
### COPY conf/phpmyadmin/config.inc.php /var/www/html/phpMyAdmin-4.8.3-english/config.inc.php



### POST-RUN commands
# For running the cron daemon:
# $ docker exec -it php-fpm-container bash -c "service cron start"
