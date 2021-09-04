FROM alpine:3.14

# Install packages
RUN apk --no-cache add \
  php8 \
  php8-fpm \
  php8-mysqli \
  php8-json \
  php8-openssl \
  php8-curl \
  php8-zlib \
  php8-xml \
  php8-phar \
  php8-intl \
  php8-dom \
  php8-xmlreader \
  php8-xmlwriter \
  php8-exif \
  php8-fileinfo \
  php8-sodium \
  php8-openssl \
  php8-gd \
  php8-simplexml \
  php8-ctype \
  php8-mbstring \
  php8-zip \
  php8-opcache \
  php8-iconv \
  php8-pecl-imagick \
  nginx \
  supervisor \
  curl \
  bash \
  less

# Create symlink so programs depending on `php` still function
RUN ln -s /usr/bin/php8 /usr/bin/php

# Configuration for services
COPY config/nginx.conf /etc/nginx/nginx.conf
COPY config/fpm-pool.conf /etc/php8/php-fpm.d/zzz_custom.conf
COPY config/php.ini /etc/php8/conf.d/zzz_custom.ini
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# wp-content volume
VOLUME /var/www/wp-content
WORKDIR /var/www/wp-content
RUN chown -R nobody.nobody /var/www
RUN mkdir -p /usr/src

# Upstream tarballs include ./wordpress/ so this gives us /usr/src/wordpress
RUN curl -o wordpress.tar.gz -SL "https://wordpress.org/latest.tar.gz" \
	&& tar -xzf wordpress.tar.gz -C /usr/src/ \
	&& rm wordpress.tar.gz \    
	&& chown -R nobody.nobody /usr/src/wordpress

# WP setup
COPY wp-config.php /usr/src/wordpress
RUN chown nobody.nobody /usr/src/wordpress/wp-config.php && chmod 640 /usr/src/wordpress/wp-config.php
COPY wp-secrets.php /usr/src/wordpress
RUN chown nobody.nobody /usr/src/wordpress/wp-secrets.php && chmod 640 /usr/src/wordpress/wp-secrets.php
COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT [ "/entrypoint.sh" ]

EXPOSE 8080

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1/wp-login.php
