FROM php:8.2-fpm

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libicu-dev \
    zip \
    libzip-dev \
    && docker-php-ext-install pdo pdo_mysql zip intl \
    && rm -rf /var/lib/apt/lists/*

# Set correct permissions
RUN usermod -u 1000 www-data \
    && groupmod -g 1000 www-data

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www/html

# Copy composer files first to leverage Docker cache
COPY composer.json composer.lock ./

# Install dependencies
RUN composer install --no-scripts --no-autoloader --no-dev

# Copy the rest of the application
COPY --chown=www-data:www-data . .

# Generate autoloader and run scripts
RUN composer dump-autoload --optimize \
    && composer run-script post-install-cmd

# Install additional dependencies
RUN composer require symfony/orm-pack doctrine/doctrine-bundle \
    && composer require doctrine/dbal \
    && composer require nelmio/cors-bundle

# Set permissions
RUN chmod -R 755 /var/www/html/public \
    && chmod -R 777 /var/www/html/var

# Copy and set permissions for entrypoint
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

EXPOSE 9000

# ENTRYPOINT ["/entrypoint.sh"]