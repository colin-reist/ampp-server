FROM php:8.2-apache

# Installer les extensions PHP nécessaires
RUN docker-php-ext-install mysqli pdo pdo_mysql

# Activer mod_rewrite
RUN a2enmod rewrite

# On ne copie rien ici, les fichiers viendront du volume
WORKDIR /var/www/html
