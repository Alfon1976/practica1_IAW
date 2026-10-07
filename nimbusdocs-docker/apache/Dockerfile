FROM php:8.2-apache

# Copiar configuración de Virtual Hosts
COPY conf/000-default.conf /etc/apache2/sites-available/000-default.conf
COPY conf/alpha-vhost.conf /etc/apache2/sites-available/alpha-vhost.conf
COPY conf/beta-vhost.conf /etc/apache2/sites-available/beta-vhost.conf

RUN a2ensite alpha-vhost.conf beta-vhost.conf

# Copiar contenidos web
COPY html/ /var/www/html/

EXPOSE 80

