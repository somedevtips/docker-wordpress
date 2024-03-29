FROM wordpress:6.1.1-apache

ARG HOST_USER_NAME
ARG HOST_USER_ID
ARG HOST_GROUP_NAME
ARG HOST_GROUP_ID

# Create e new groups the same as the host group, add host user to it and create home for host user
RUN groupadd -g "${HOST_GROUP_ID}" "${HOST_GROUP_NAME}" && \
    useradd -g ${HOST_GROUP_ID} -u ${HOST_USER_ID} -m ${HOST_USER_NAME}

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
COPY ./config/php/zcustom.ini $PHP_INI_DIR/conf.d/
COPY ./config/php/xdebug.ini $PHP_INI_DIR/conf.d/

# Custom Apache configuration
COPY ./config/apache/wordpress-site.conf $APACHE_CONFDIR/sites-available/
RUN a2dissite 000-default.conf
RUN a2ensite wordpress-site.conf