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

# Cambiar permisos antes de ejecutar Composer
RUN chown -R root:root /var/www/html && chmod -R 777 /var/www/html

# Copy composer files first to leverage Docker cache
COPY composer.json composer.lock ./

# Copy .env file
COPY .env ./
RUN chmod 644 .env

# Install dependencies como root
RUN COMPOSER_ALLOW_SUPERUSER=1 composer install --no-scripts --no-autoloader --no-dev

# Copiar el resto de la aplicación
COPY . .

# Restaurar permisos adecuados después de la instalación
RUN chown -R www-data:www-data /var/www/html && chmod -R 775 /var/www/html

# Set permissions for specific directories
RUN chmod -R 755 /var/www/html/public \
    && chmod -R 777 /var/www/html/var

# Copy and set permissions for entrypoint
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

EXPOSE 9000
