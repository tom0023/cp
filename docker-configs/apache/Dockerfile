# image
FROM php:7.4-apache

RUN usermod -u 1000 www-data \
    && groupmod -g 1000 www-data

RUN apt-get update && apt-get install -y \
  libssl-dev \
  openssl \
  vim \
  wget \
  curl \
  ssl-cert \
  wget \
  libjpeg-dev \
  libfreetype6-dev \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* \
  && a2enmod ssl \
  && a2ensite default-ssl \
  && a2enmod rewrite

RUN apt-get update && apt-get install -y \
  libmagick++-dev \
  libmagickwand-dev \
  libpq-dev \
  libfreetype6-dev \
  libjpeg62-turbo-dev \
  libpng-dev \
  libwebp-dev \
  libxpm-dev

RUN docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/
RUN docker-php-ext-install -j$(nproc) gd

RUN curl -sSL https://github.com/mailhog/mhsendmail/releases/download/v0.2.0/mhsendmail_linux_amd64 -o mhsendmail \
    && chmod +x mhsendmail \
    && mv mhsendmail /usr/local/bin/mhsendmail

EXPOSE 80
EXPOSE 443

ENV APP_ROOT /var/www/web
ENV APACHE_DOCUMENT_ROOT /var/www/web/htdocs
ENV APACHE_LOG_DIR /var/log/apache2
ENV WP_UPLOAD_DIR /var/www/web/htdocs/uploads

# Set php.ini
COPY ./etc/php.d/20-overwride.ini /usr/local/etc/php/conf.d/

# Set apache conf
COPY ./apache2.conf /etc/apache2/
COPY ./sites/*.conf /etc/apache2/sites-available/
COPY ./ssl/* /etc/apache2/

# Install php module
RUN docker-php-ext-install pdo_mysql
RUN docker-php-ext-install mysqli

CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]

#RUN echo MYSQL_USERNAME=${MYSQL_USERNAME} >> ~/.bash_profile
