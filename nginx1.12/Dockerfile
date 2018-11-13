FROM nginx:1.12-alpine

# Generate a self-signed cert
RUN apk add --no-cache openssl && mkdir -p /etc/nginx/ssl \
	&& openssl req -batch -x509 -newkey rsa:4096 -days 3650 -nodes -sha256 -subj "/" \
		-keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt \
	&& apk del openssl

# Configure environment variables
ENV \
	APACHE_DOCUMENTROOT=/var/www/docroot \
	APACHE_FCGI_HOST_PORT="cli:9000"

RUN set -x \
    # Link out custom docroot location to the default htdocs folder by default
    && mkdir -p /var/www \
    && ln -s /usr/share/nginx/html $APACHE_DOCUMENTROOT

COPY httpd-foreground /usr/local/bin/
COPY nginx /etc/nginx
COPY healthcheck.sh /opt/healthcheck.sh

WORKDIR /var/www

EXPOSE 80 443

CMD ["httpd-foreground"]

# Health check script
HEALTHCHECK --interval=5s --timeout=1s --retries=12 CMD ["/opt/healthcheck.sh"]
