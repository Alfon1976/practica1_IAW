FROM httpd:2.4-bookworm

# Copiar configuración personalizada de Apache
COPY apache/nimbusdocs.conf /usr/local/apache2/conf/httpd.conf

# Copiar las dos webs
COPY web/apachedocs/ /var/www/apachedocs/
COPY web/nginxdocs/ /var/www/nginxdocs/
COPY web/404/ /var/www/404/
# Crear directorios de logs
RUN mkdir -p /var/log/apache2/nimbusdocs/apachedocs \
    /var/log/apache2/nimbusdocs/nginxdocs

EXPOSE 80
