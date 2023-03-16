FROM wordpress:6.1.1-apache

# Install WP CLI and other tools. mariadb-client is used by 'wp db' commands
RUN apt update \
   && apt dist-upgrade -y \
   && apt install -y less nano wget \
   && wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
   && chmod +x wp-cli.phar \
   && mv wp-cli.phar /usr/local/bin/wp \
   && apt install -y mariadb-client

# Install Xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug

# Composer
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer
ENV COMPOSER_ALLOW_SUPERUSER 1

# Custom PHP configuration
COPY ./php/zcustom.ini $PHP_INI_DIR/conf.d/
COPY ./php/xdebug.ini $PHP_INI_DIR/conf.d/

# Custom Apache configuration
COPY apache/wordpress-site.conf $APACHE_CONFDIR/sites-available/
RUN a2dissite 000-default.conf
RUN a2ensite wordpress-site.conf