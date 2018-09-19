FROM php:7.2-apache-stretch

RUN a2enmod rewrite

# install the PHP extensions we need
RUN apt-get update \
    && apt-get install -y git-core libsqlite3-dev libpq-dev libmcrypt-dev libpng-dev libjpeg-dev && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
    && docker-php-ext-install gd mysqli zip mbstring pdo pdo_mysql pdo_sqlite pdo_pgsql json \
    && pecl install mcrypt-1.0.1 && docker-php-ext-enable mcrypt.so

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=60'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

# APACHE CUSTOM USER
RUN useradd -ms /bin/bash martin \
    && sed -i -e 's/www-data/martin/g' /etc/apache2/envvars \
    && sed -i -e 's/www-data/martin/g' /etc/apache2/apache2.conf

VOLUME /var/www/html

RUN curl -sS https://getcomposer.org/installer | php \
        && mv composer.phar /usr/local/bin/composer

RUN mkdir /usr/src/october && cd /usr/src/october && curl -s https://octobercms.com/api/installer | php

COPY config/*.php /usr/src/october/config/

RUN chown -R www-data:www-data /usr/src/october

COPY docker-entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]

CMD ["apache2-foreground"]
