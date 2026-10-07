FROM nginx:alpine

COPY conf/nimbusdocs.conf /etc/nginx/conf.d/default.conf
COPY ssl/nimbusdocs.crt /etc/nginx/ssl/nimbusdocs.crt
COPY ssl/nimbusdocs.key /etc/nginx/ssl/nimbusdocs.key

EXPOSE 80 443