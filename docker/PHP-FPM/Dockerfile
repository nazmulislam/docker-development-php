FROM php:7.4-fpm
MAINTAINER Nazmul Islam <nislam@ethixbase.com>
# Locales
RUN apt-get update \
	&& apt-get install -y locales

# installs xdebug
#WORKDIR "/"
#RUN apt-get install wget -y
#RUN wget https://xdebug.org/files/xdebug-$xdebugversion.tgz && tar -xvzf xdebug-*.tgz
#WORKDIR "/var/www/html"
#RUN cd /xdebug-* && phpize && ./configure --enable-xdebug && make && make install
RUN apt-get update && apt-get install -y wkhtmltopdf xvfb

RUN dpkg-reconfigure locales \
	&& locale-gen C.UTF-8 \
	&& /usr/sbin/update-locale LANG=C.UTF-8

RUN echo 'en_US.UTF-8 UTF-8' >> /etc/locale.gen \
	&& locale-gen

ENV LC_ALL C.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

RUN apt-get update && apt-get install -y \
        openssl \
        git \
        gnupg2


RUN pecl install redis-4.0.1 \
    && docker-php-ext-enable redis

# intl
RUN apt-get update \
	&& apt-get install -y libicu-dev \
	&& docker-php-ext-configure intl \
	&& docker-php-ext-install -j$(nproc) \
                intl \
                pcntl

# xml
RUN apt-get update \
	&& apt-get install -y \
	libxml2-dev \
	libxslt-dev \
	&& docker-php-ext-install -j$(nproc) \
		dom \
		xmlrpc \
		xsl

# images
RUN apt-get update \
	&& apt-get install -y \
	libfreetype6-dev \
	libjpeg62-turbo-dev \
	libpng-dev \
    libonig-dev \
    libzip-dev \
	libgd-dev \
	&& docker-php-ext-configure gd \
	&& docker-php-ext-install -j$(nproc) \
		gd \
		exif

# database
RUN docker-php-ext-install -j$(nproc) \
	mysqli \
	pdo \
	pdo_mysql
# strings
RUN docker-php-ext-install -j$(nproc) \
	gettext \
	mbstring
# math
RUN apt-get update \
	&& apt-get install -y libgmp-dev \
	&& ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h \
	&& docker-php-ext-install -j$(nproc) \
		gmp \
		bcmath

# compression
RUN apt-get update \
	&& apt-get install -y \
	libbz2-dev \
	zlib1g-dev \
	&& docker-php-ext-install -j$(nproc) \
		zip \
		bz2

# ssh2
RUN apt-get update \
	&& apt-get install -y \
	libssh2-1-dev

# others
RUN docker-php-ext-install -j$(nproc) \
	soap \
	sockets \
	calendar \
	sysvmsg \
	sysvsem \
	sysvshm


# xdebug
#RUN pecl install xdebug-2.6.1 \
#    && docker-php-ext-enable xdebug

ENV COMPOSER_VERSION 1.7.2
RUN curl -o /tmp/composer-setup.php https://getcomposer.org/installer \
  && curl -o /tmp/composer-setup.sig https://composer.github.io/installer.sig \
  && php -r "if (hash('SHA384', file_get_contents('/tmp/composer-setup.php')) !== trim(file_get_contents('/tmp/composer-setup.sig'))) { unlink('/tmp/composer-setup.php'); echo 'Invalid installer' . PHP_EOL; exit(1); }"

RUN php /tmp/composer-setup.php --no-ansi --install-dir=/usr/local/bin --filename=composer --version=${COMPOSER_VERSION} && rm -rf /tmp/composer-setup.php

# Clean
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /var/cache/*

