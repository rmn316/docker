ARG PHP_VERSION=7.2
FROM php:${PHP_VERSION}-fpm-alpine

LABEL maintainer="Robert Neal <rmn316@gmail.com>"

RUN apk update \
    && apk upgrade \
    && apk add --update --no-cache \
        autoconf \
        g++ \
        gcc \
        musl-dev \
        make \
        curl

###########################################################################
# BZ2:
###########################################################################
ARG INSTALL_BZ2=true
RUN if [ ${INSTALL_BZ2} = true ]; then \
  apk add --update --no-cache bzip2-dev \
  && docker-php-ext-install bz2 \
;fi

###########################################################################
# Enchant:
###########################################################################
#BROKEN
ARG INSTALL_ENCHANT=false
RUN if [ ${INSTALL_ENCHANT} = true ]; then \
  apk add --update --no-cache enchant2 \
  && docker-php-ext-install enchant \
  && php -m | grep -oiE '^enchant$' \
;fi

###########################################################################
# GMP (GNU Multiple Precision):
###########################################################################
ARG INSTALL_GMP=true
RUN if [ ${INSTALL_GMP} = true ]; then \
    # Install the GMP extension
	apk add --update --no-cache gmp-dev \
    && docker-php-ext-install gmp \
;fi

###########################################################################
# GnuPG:
###########################################################################
ARG INSTALL_GNUPG=true
RUN if [ ${INSTALL_GNUPG} = true ]; then \
  apk add --update --no-cache gpgme-dev \
  && \
#      if [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]; then \
#        pecl install gnupg-1.5.0 \
#      else \
   pecl install gnupg \
#      ;fi \
  && docker-php-ext-enable gnupg \
  && php -m | grep -q 'gnupg' \
;fi

###########################################################################
# SSH2:
###########################################################################
ARG INSTALL_SSH2=true
RUN if [ ${INSTALL_SSH2} = true ]; then \
    # Install the ssh2 extension
    apk add --update --no-cache libssh2-dev \
    && pecl install -a ssh2-1.3.1 \
    && docker-php-ext-enable ssh2 \
;fi

###########################################################################
# SOAP:
###########################################################################
ARG INSTALL_SOAP=true
RUN if [ ${INSTALL_SOAP} = true ]; then \
    # Install the soap extension
    apk add --update --no-cache libxml2-dev php-soap \
    && docker-php-ext-install soap \
;fi

###########################################################################
# XSL:
###########################################################################
ARG INSTALL_XSL=true
RUN if [ ${INSTALL_XSL} = true ]; then \
    # Install the xsl extension
    apk add --update --no-cache libxslt-dev \
    && docker-php-ext-install xsl \
;fi

###########################################################################
# pgsql
###########################################################################
ARG INSTALL_PGSQL=true
RUN if [ ${INSTALL_PGSQL} = true ]; then \
    # Install the pgsql extension
    apk add --update --no-cache postgresql-dev \
    && docker-php-ext-install pdo pgsql pdo_pgsql \
    # pdo_pgsql ?
;fi

###########################################################################
# pgsql client # SHOULD NOT REQUIRE HERE
###########################################################################
#ARG INSTALL_PG_CLIENT=false
#ARG INSTALL_POSTGIS=false
#RUN if [ ${INSTALL_PG_CLIENT} = true ]; then \
#    apt-get install -yqq gnupg \
#    && . /etc/os-release \
#    && echo "deb http://apt.postgresql.org/pub/repos/apt $VERSION_CODENAME-pgdg main" > /etc/apt/sources.list.d/pgdg.list \
#    && curl -sL https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
#    && apt-get update -yqq \
#    && apt-get install -yqq postgresql-client-12 postgis; \
#    if [ ${INSTALL_POSTGIS} = true ]; then \
#      apt-get install -yqq postgis; \
#    fi \
#    && apt-get purge -yqq gnupg \
#;fi

###########################################################################
# xDebug:
###########################################################################
ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
  # Install the xdebug extension
#  if [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]; then \
    pecl install xdebug \
#  else \
#    pecl install xdebug-2.9.8; \
#  fi \
  && docker-php-ext-enable xdebug \
;fi

# Copy xdebug configuration for remote debugging
COPY ./conf/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

RUN if [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]; then \
  sed -i "s/xdebug.remote_host=/xdebug.client_host=/" /usr/local/etc/php/conf.d/xdebug.ini && \
  sed -i "s/xdebug.remote_connect_back=0/xdebug.discover_client_host=false/" /usr/local/etc/php/conf.d/xdebug.ini && \
  sed -i "s/xdebug.remote_port=9000/xdebug.client_port=9003/" /usr/local/etc/php/conf.d/xdebug.ini && \
  sed -i "s/xdebug.profiler_enable=0/; xdebug.profiler_enable=0/" /usr/local/etc/php/conf.d/xdebug.ini && \
  sed -i "s/xdebug.profiler_output_dir=/xdebug.output_dir=/" /usr/local/etc/php/conf.d/xdebug.ini && \
  sed -i "s/xdebug.remote_mode=req/; xdebug.remote_mode=req/" /usr/local/etc/php/conf.d/xdebug.ini && \
  sed -i "s/xdebug.remote_autostart=0/xdebug.start_with_request=yes/" /usr/local/etc/php/conf.d/xdebug.ini && \
  sed -i "s/xdebug.remote_enable=0/xdebug.mode=debug/" /usr/local/etc/php/conf.d/xdebug.ini \
;else \
  sed -i "s/xdebug.remote_autostart=0/xdebug.remote_autostart=1/" /usr/local/etc/php/conf.d/xdebug.ini && \
  sed -i "s/xdebug.remote_enable=0/xdebug.remote_enable=1/" /usr/local/etc/php/conf.d/xdebug.ini \
;fi
RUN sed -i "s/xdebug.cli_color=0/xdebug.cli_color=1/" /usr/local/etc/php/conf.d/xdebug.ini

###########################################################################
# pcov:
###########################################################################
ARG INSTALL_PCOV=true
RUN if [ ${INSTALL_PCOV} = true ]; then \
  if [ $(php -r "echo PHP_MAJOR_VERSION;") = "7" ]; then \
    pecl install pcov \
    && docker-php-ext-enable pcov \
  ;fi \
;fi

###########################################################################
# Phpdbg:
###########################################################################
ARG INSTALL_PHPDBG=true
RUN if [ ${INSTALL_PHPDBG} = true ]; then \
    # Load the xdebug extension only with phpunit commands
    apk add --update --no-cache php$(php -r "echo PHP_MAJOR_VERSION;")-phpdbg \
;fi

###########################################################################
# Blackfire:
###########################################################################
ARG INSTALL_BLACKFIRE=true
RUN if [ ${INSTALL_XDEBUG} = false -a ${INSTALL_BLACKFIRE} = true ]; then \
    version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/amd64/$version \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp \
    && mv /tmp/blackfire-*.so $(php -r "echo ini_get('extension_dir');")/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > $PHP_INI_DIR/conf.d/blackfire.ini \
;fi

###########################################################################
# Xhprof:
###########################################################################
ARG INSTALL_XHPROF=true
RUN if [ ${INSTALL_XHPROF} = true ]; then \
    echo 'HOW TO DO ON ALPINE' \
;fi

###########################################################################
# PHP REDIS EXTENSION
###########################################################################
ARG INSTALL_PHPREDIS=true
RUN if [ ${INSTALL_PHPREDIS} = true ]; then \
    # Install Php Redis Extension
    pecl install -o -f redis \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis \
;fi

###########################################################################
# Swoole EXTENSION
###########################################################################
ARG INSTALL_SWOOLE=true
RUN if [ ${INSTALL_SWOOLE} = true ]; then \
    pecl install swoole \
    && docker-php-ext-enable swoole \
    && php -m | grep -q 'swoole' \
;fi

###########################################################################
# Taint EXTENSION
###########################################################################
ARG INSTALL_TAINT=true
RUN if [ ${INSTALL_TAINT} = true ]; then \
    # Install Php TAINT Extension
#    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "7" ]; then \
      pecl install taint \
      && docker-php-ext-enable taint \
      && php -m | grep -q 'taint' \
#    fi \
;fi

###########################################################################
# MongoDB:
###########################################################################
ARG INSTALL_MONGO=true
RUN if [ ${INSTALL_MONGO} = true ]; then \
    # Install the mongodb extension
    pecl install mongodb \
    && docker-php-ext-enable mongodb \
;fi

###########################################################################
# AMQP:
###########################################################################
ARG INSTALL_AMQP=true
RUN if [ ${INSTALL_AMQP} = true ]; then \
    # Install the amqp extension
    apk add --update --no-cache rabbitmq-c-dev \
#      if [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]; then \
#            pecl install amqp-1.11.0beta; \
#      else \
    && pecl install amqp \
#      fi; \
    && docker-php-ext-enable amqp \
    && php -m | grep -oiE '^amqp$' \
;fi

###########################################################################
# CASSANDRA:
###########################################################################
#ARG INSTALL_CASSANDRA=false
#RUN if [ ${INSTALL_CASSANDRA} = true ]; then \
#    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]; then \
#      echo "PHP Driver for Cassandra is not supported for PHP 8.0."; \
#    else \
#      apt-get install libgmp-dev -yqq && \
#      curl https://downloads.datastax.com/cpp-driver/ubuntu/18.04/dependencies/libuv/v1.35.0/libuv1-dev_1.35.0-1_amd64.deb -o libuv1-dev.deb && \
#      curl https://downloads.datastax.com/cpp-driver/ubuntu/18.04/dependencies/libuv/v1.35.0/libuv1_1.35.0-1_amd64.deb -o libuv1.deb && \
#      curl https://downloads.datastax.com/cpp-driver/ubuntu/18.04/cassandra/v2.16.0/cassandra-cpp-driver-dev_2.16.0-1_amd64.deb -o cassandra-cpp-driver-dev.deb && \
#      curl https://downloads.datastax.com/cpp-driver/ubuntu/18.04/cassandra/v2.16.0/cassandra-cpp-driver_2.16.0-1_amd64.deb -o cassandra-cpp-driver.deb && \
#      dpkg -i libuv1.deb && \
#      dpkg -i libuv1-dev.deb && \
#      dpkg -i cassandra-cpp-driver.deb && \
#      dpkg -i cassandra-cpp-driver-dev.deb && \
#      rm libuv1.deb libuv1-dev.deb cassandra-cpp-driver-dev.deb cassandra-cpp-driver.deb && \
#      cd /usr/src && \
#      git clone https://github.com/datastax/php-driver.git && \
#      cd /usr/src/php-driver/ext && \
#      phpize && \
#      mkdir /usr/src/php-driver/build && \
#      cd /usr/src/php-driver/build && \
#      ../ext/configure > /dev/null && \
#      make clean > /dev/null && \
#      make > /dev/null 2>&1 && \
#      make install && \
#      echo "extension=cassandra.so" >> /etc/php/${LARADOCK_PHP_VERSION}/mods-available/cassandra.ini && \
#      ln -s /etc/php/${LARADOCK_PHP_VERSION}/mods-available/cassandra.ini /etc/php/${LARADOCK_PHP_VERSION}/cli/conf.d/30-cassandra.ini; \
#    fi \
#;fi

###########################################################################
# GEARMAN:
###########################################################################
ARG INSTALL_GEARMAN=false
#RUN if [ ${INSTALL_GEARMAN} = true ]; then \
#    apt-get -y install libgearman-dev && \
#    cd /tmp && \
#    curl -L https://github.com/wcgallego/pecl-gearman/archive/gearman-2.0.5.zip -O && \
#    unzip gearman-2.0.5.zip && \
#    mv pecl-gearman-gearman-2.0.5 pecl-gearman && \
#    cd /tmp/pecl-gearman && \
#    phpize && \
#    ./configure && \
#    make -j$(nproc) && \
#    make install && \
#    cd / && \
#    rm /tmp/gearman-2.0.5.zip && \
#    rm -r /tmp/pecl-gearman && \
#    docker-php-ext-enable gearman \
#;fi

###########################################################################
# bcmath:
###########################################################################
ARG INSTALL_BCMATH=true
RUN if [ ${INSTALL_BCMATH} = true ]; then \
    # Install the bcmath extension
    docker-php-ext-install bcmath \
;fi

###########################################################################
# PHP Memcached:
###########################################################################
ARG INSTALL_MEMCACHED=true
RUN if [ ${INSTALL_MEMCACHED} = true ]; then \
    # Install the php memcached extension
    apk add --update --no-cache libmemcached-dev \
    && pecl install memcached \
    && docker-php-ext-enable memcached \
;fi

###########################################################################
# Exif:
###########################################################################
ARG INSTALL_EXIF=true
RUN if [ ${INSTALL_EXIF} = true ]; then \
    # Enable Exif PHP extentions requirements
    docker-php-ext-install exif \
;fi

###########################################################################
# PHP Aerospike:
###########################################################################
ARG INSTALL_AEROSPIKE=true
RUN if [ ${INSTALL_AEROSPIKE} = true ]; then \
    echo 'NOT SUPPORTED ON ALPINE :(' \
;fi

###########################################################################
# PHP OCI8:
###########################################################################
ARG INSTALL_OCI8=false

###########################################################################
# IonCube Loader:
###########################################################################
ARG INSTALL_IONCUBE=false

###########################################################################
# Opcache:
###########################################################################
ARG INSTALL_OPCACHE=true
RUN if [ ${INSTALL_OPCACHE} = true ]; then \
    docker-php-ext-install opcache \
;fi

# Copy opcache configration
COPY ./conf/opcache.ini /usr/local/etc/php/conf.d/opcache.ini

###########################################################################
# Mysql [PDO]:
###########################################################################
ARG INSTALL_MYSQL=true
RUN if [ ${INSTALL_MYSQL} = true ]; then \
    docker-php-ext-install pdo_mysql \
;fi

###########################################################################
# Mysqli Modifications:
###########################################################################
ARG INSTALL_MYSQLI=true

RUN if [ ${INSTALL_MYSQLI} = true ]; then \
    docker-php-ext-install mysqli \
;fi

###########################################################################
# Human Language and Character Encoding Support:
###########################################################################
ARG INSTALL_INTL=true
RUN if [ ${INSTALL_INTL} = true ]; then \
    # Install intl and requirements
    apk add --update --no-cache zlib-dev icu-dev \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
;fi

###########################################################################
# GHOSTSCRIPT:
###########################################################################
ARG INSTALL_GHOSTSCRIPT=true
RUN if [ ${INSTALL_GHOSTSCRIPT} = true ]; then \
    # Install the ghostscript extension
    # for PDF editing
    apk add --update --no-cache poppler-utils ghostscript \
;fi

###########################################################################
# LDAP:
###########################################################################
ARG INSTALL_LDAP=true
RUN if [ ${INSTALL_LDAP} = true ]; then \
    apk add --update --no-cache icu-dev openldap-dev \
    && docker-php-ext-install ldap \
;fi

###########################################################################
# SQL SERVER:
###########################################################################
ARG INSTALL_MSSQL=false

###########################################################################
# Image optimizers:
###########################################################################
ARG INSTALL_IMAGE_OPTIMIZERS=true
RUN if [ ${INSTALL_IMAGE_OPTIMIZERS} = true ]; then \
    apk add --update --no-cache jpegoptim optipng pngquant gifsicle \
;fi

###########################################################################
# ImageMagick:
###########################################################################
ARG INSTALL_IMAGEMAGICK=true
ARG IMAGEMAGICK_VERSION=latest
ENV IMAGEMAGICK_VERSION ${IMAGEMAGICK_VERSION}
RUN if [ ${INSTALL_IMAGEMAGICK} = true ]; then \
# FIXES NEEDED FOR PHP8
    apk add --update --no-cache imagemagick-dev \
#    && \
#    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]; then \
#      && cd /tmp \
#      if [ ${IMAGEMAGICK_VERSION} = "latest" ]; then \
#        && git clone https://github.com/Imagick/imagick \
#      else \
#        && git clone --branch ${IMAGEMAGICK_VERSION} https://github.com/Imagick/imagick \
#      ;fi \
#      && cd imagick \
#      && phpize \
#      && ./configure \
#      && make \
#      && make install \
#      && rm -r /tmp/imagick \
#    ;else \
      && pecl install imagick \
#    ;fi \
    && docker-php-ext-enable imagick \
    && php -m | grep -q 'imagick' \
;fi

###########################################################################
# IMAP:
###########################################################################
ARG INSTALL_IMAP=true
RUN if [ ${INSTALL_IMAP} = true ]; then \
    apk add --update --no-cache imap-dev krb5-dev \
    && docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install imap \
;fi

###########################################################################
# Calendar:
###########################################################################
ARG INSTALL_CALENDAR=true
RUN if [ ${INSTALL_CALENDAR} = true ]; then \
    docker-php-ext-configure calendar \
    && docker-php-ext-install calendar \
;fi

###########################################################################
# APCU:
###########################################################################
ARG INSTALL_APCU=true
RUN if [ ${INSTALL_APCU} = true ]; then \
    pecl install apcu \
    && docker-php-ext-enable apcu \
;fi

###########################################################################
# YAML:
###########################################################################
ARG INSTALL_YAML=true
RUN if [ ${INSTALL_YAML} = true ]; then \
    apk add --update --no-cache yaml-dev \
    && pecl install yaml \
    && docker-php-ext-enable yaml \
;fi

###########################################################################
# RDKAFKA:
###########################################################################
ARG INSTALL_RDKAFKA=true
RUN if [ ${INSTALL_RDKAFKA} = true ]; then \
    apk add --update --no-cache librdkafka-dev \
    && pecl install rdkafka \
    && docker-php-ext-enable rdkafka \
;fi

###########################################################################
# FFMPEG:
###########################################################################
ARG INSTALL_FFMPEG=true
RUN if [ ${INSTALL_FFMPEG} = true ]; then \
    apk add --update --no-cache ffmpeg \
;fi

###########################################################################
# GETTEXT:
###########################################################################
ARG INSTALL_GETTEXT=true
RUN if [ ${INSTALL_GETTEXT} = true ]; then \
    apk add --update --no-cache  zlib-dev icu-dev g++ libpq libressl-dev gettext \
    && docker-php-ext-install gettext \
;fi

###########################################################################
# Install additional locales:
###########################################################################
ARG INSTALL_ADDITIONAL_LOCALES=false
ARG ADDITIONAL_LOCALES
# UPDATE TO WORK ON ALPINE
#RUN if [ ${INSTALL_ADDITIONAL_LOCALES} = true ]; then \
#    apk add --update --no-cache musl-locales \
#    && echo '' >> /usr/share/locale/locale.alias \
#    && temp="${ADDITIONAL_LOCALES%\"}" \
#    && temp="${temp#\"}" \
#    && for i in ${temp}; do sed -i "/$i/s/^#//g" /etc/locale.gen; done \
#    && locale-gen \
#;fi

###########################################################################
# Mailparse extension:
###########################################################################
ARG INSTALL_MAILPARSE=true
RUN if [ ${INSTALL_MAILPARSE} = true ]; then \
    # Install mailparse extension
    pecl install -o -f mailparse \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable mailparse \
;fi


###########################################################################
# CacheTool:
###########################################################################
ARG INSTALL_CACHETOOL=true
RUN if [ ${INSTALL_CACHETOOL} = true ]; then \
    curl -sO http://gordalina.github.io/cachetool/downloads/cachetool.phar \
    && chmod +x cachetool.phar \
    && mv cachetool.phar /usr/local/bin/cachetool \
;fi

###########################################################################
# XMLRPC:
###########################################################################
ARG INSTALL_XMLRPC=true
RUN if [ ${INSTALL_XMLRPC} = true ]; then \
  apk add --update --no-cache xml2-dev; \
  if [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]; then \
    pecl install xmlrpc-1.0.0RC2; \
    docker-php-ext-enable xmlrpc; \
  else \
    docker-php-ext-install xmlrpc; \
  fi \
;fi

###########################################################################
# PHP DECIMAL:
###########################################################################
USER root
ARG INSTALL_PHPDECIMAL=false
RUN if [ ${INSTALL_PHPDECIMAL} = true ]; then \
    # problem install mpdecimal ...
#    apk add --update --no-cache mpdecimal \
    pecl install decimal \
    && docker-php-ext-enable decimal \
    && php -m | grep -q 'decimal' \
;fi

###########################################################################
# zookeeper
###########################################################################
ARG INSTALL_ZOOKEEPER=false

RUN if [ ${INSTALL_ZOOKEEPER} = true ]; then \
    apt install -yqq libzookeeper-mt-dev; \
    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "8" ]; then \
      curl -L -o /tmp/php-zookeeper.tar.gz https://github.com/php-zookeeper/php-zookeeper/archive/master.tar.gz; \
      mkdir -p /tmp/php-zookeeper; \
      tar -C /tmp/php-zookeeper -zxvf /tmp/php-zookeeper.tar.gz --strip 1; \
      cd /tmp/php-zookeeper; \
      phpize && ./configure && make && make install;\
    else \
        pecl install zookeeper-0.7.2; \
    fi; \
    && docker-php-ext-enable zookeeper \
    && php -m | grep -q 'zookeeper' \
;fi

###########################################################################
# Check PHP version:
###########################################################################
RUN set -xe; php -v | head -n 1 | grep -q "PHP ${PHP_VERSION}."

RUN echo php -m

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000
