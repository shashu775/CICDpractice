FROM php:7.4-apache

# 1. Install necessary extensions for PDO and MySQLi
RUN docker-php-ext-install mysqli pdo_mysql && docker-php-ext-enable mysqli pdo_mysql

# 2. Create the target directory inside the container
RUN mkdir -p /var/www/html/api

# 3. COPY the files from your current local directory (.) 
# into the container's /api folder
COPY . /var/www/html/api/

# 4. Set permissions for the Apache user
RUN chown -R www-data:www-data /var/www/html

EXPOSE 80