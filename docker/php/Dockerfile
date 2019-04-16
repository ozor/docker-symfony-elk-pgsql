FROM php:7.2-fpm

RUN apt-get update \
  && apt-get install -y \
        libzip-dev \
        zip \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        zlib1g-dev libicu-dev \
  && apt-get update && apt-get install -y libpq-dev \
  && docker-php-ext-configure zip --with-libzip \
  && docker-php-ext-install zip \
  && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
  && docker-php-ext-install -j$(nproc) gd \
  && docker-php-ext-configure intl \
  && docker-php-ext-install intl \
  && docker-php-ext-enable intl \
  && docker-php-ext-install bcmath \
  && docker-php-ext-install pdo pdo_pgsql

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
  && composer --version

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone \
  && printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini \
  && "date"

# install xdebug
RUN pecl install xdebug \
  && docker-php-ext-enable xdebug \
  && echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "xdebug.remote_connect_back=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "xdebug.idekey=\"PHPSTORM\"" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "xdebug.remote_port=9001" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini


RUN echo 'alias sf="php app/console"' >> ~/.bashrc \
  && echo 'alias sf3="php bin/console"' >> ~/.bashrc

RUN usermod -u 1000 www-data && chown -R www-data:www-data /var/www