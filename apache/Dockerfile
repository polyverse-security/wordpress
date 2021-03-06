FROM php:5.6-apache

RUN a2enmod rewrite

# install the PHP extensions we need
RUN apt-get update && apt-get install -y libpng12-dev libjpeg-dev vim && rm -rf /var/lib/apt/lists/* \
	&& docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
	&& docker-php-ext-install gd
RUN docker-php-ext-install mysqli

VOLUME /var/www/html

#ENV WORDPRESS_VERSION 4.3.1
ENV WORDPRESS_VERSION 2.0
#ENV WORDPRESS_SHA1 b2e5652a6d2333cabe7b37459362a3e5b8b66221
ENV WORDPRESS_SHA1 f27c04a9d94b254522b656c24e2de1bde2408641

# upstream tarballs include ./wordpress/ so this gives us /usr/src/wordpress
RUN curl -o wordpress.tar.gz -SL https://wordpress.org/wordpress-${WORDPRESS_VERSION}.tar.gz \
	&& echo "$WORDPRESS_SHA1 *wordpress.tar.gz" | sha1sum -c - \
	&& tar -xzf wordpress.tar.gz -C /usr/src/ \
	&& rm wordpress.tar.gz \
	&& chown -R www-data:www-data /usr/src/wordpress

COPY docker-entrypoint.sh /entrypoint.sh
COPY wp-db.php /usr/src/wordpress/wp-includes/wp-db.php
COPY upgrade-schema.php /usr/src/wordpress/wp-admin/upgrade-schema.php

# grr, ENTRYPOINT resets CMD now
ENTRYPOINT ["/entrypoint.sh"]
CMD ["apache2-foreground"]
