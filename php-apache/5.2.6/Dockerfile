# Use an official base image
FROM debian:8-slim

# Set the maintainer
LABEL maintainer="weeaix@gmail.com"

ENV VERSION_APACHE 2.2.34
ENV VERSION_PHP 5.2.6

ENV PHP_INI_DIR /etc/php

RUN mkdir -p $PHP_INI_DIR/conf.d

# Update the system and install dependencies
RUN rm /etc/apt/sources.list
RUN echo "deb http://archive.debian.org/debian-security jessie/updates main" > /etc/apt/sources.list.d/jessie.list
RUN echo "deb http://archive.debian.org/debian jessie main" >> /etc/apt/sources.list.d/jessie.list
RUN apt-get update
RUN apt-get install -y --force-yes build-essential libxml2-dev libmysqlclient-dev curl libcurl4-openssl-dev libssl-dev libjpeg-dev libpng12-dev && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

RUN ln -s /usr/lib/x86_64-linux-gnu/libjpeg.a /usr/lib/libjpeg.a \
    && ln -s /usr/lib/x86_64-linux-gnu/libjpeg.so /usr/lib/libjpeg.so \
    && ln -s /usr/lib/x86_64-linux-gnu/libpng.a /usr/lib/libpng.a \
    && ln -s /usr/lib/x86_64-linux-gnu/libpng.so /usr/lib/libpng.so \
    && ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient.so /usr/lib/libmysqlclient.so \
    && ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient.a /usr/lib/libmysqlclient.a \
    && ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient_r.so /usr/lib/libmysqlclient_r.so \
    && ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient_r.a /usr/lib/libmysqlclient_r.a

RUN cd /usr/src && \
    curl https://archive.apache.org/dist/httpd/httpd-$VERSION_APACHE.tar.gz -O && \
    tar -zxvf httpd-$VERSION_APACHE.tar.gz && \
    rm httpd-$VERSION_APACHE.tar.gz && \
    cd httpd-$VERSION_APACHE && \
    ./configure --enable-mods-shared="all" --enable-so --enable-rewrite --enable-headers  \
    --enable-log_config --enable-logio --enable-prefork --enable-http_core \
    --enable-so --enable-actions --enable-alias --enable-autoindex --enable-cache \
    --enable-cgi --enable-deflate --enable-dir --enable-env --enable-expires \
    --enable-geoip --enable-mime --enable-negotiation \
    --enable-setenvif  --enable-status --enable-authz_host && \
    make && make install

COPY php-5.2.6.tar.gz /usr/src/php-5.2.6.tar.gz
COPY patch/php5.2.6-libxml2-newbuffer.patch /tmp/php5.2.6-libxml2-newbuffer.patch
RUN cd /usr/src && \
    # curl http://museum.php.net/php5/php-$VERSION_PHP.tar.gz -O && \
    tar -zxvf php-$VERSION_PHP.tar.gz && \
    rm php-$VERSION_PHP.tar.gz && \
    mv php-$VERSION_PHP php && \
    cd php && \
    cat /tmp/php5.2.6-libxml2-newbuffer.patch | patch -p1 && \
    ./configure  --with-config-file-path="$PHP_INI_DIR" \
		--with-config-file-scan-dir="$PHP_INI_DIR/conf.d" \
        --with-apxs2=/usr/local/apache2/bin/apxs \
        --with-curl \
        --with-gd \
		--with-mysql \
        --with-pdo-mysql \
        --without-iconv \
        --enable-mbstring \
        --enable-zip && \
    make && make install

COPY conf/httpd.conf /usr/local/apache2/conf/httpd.conf
COPY conf/php.ini /etc/php/php.ini

# Expose port 80 for Apache
EXPOSE 80

# Start Apache in the foreground
CMD ["/usr/local/apache2/bin/apachectl", "-D", "FOREGROUND"]
