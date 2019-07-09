# Dockerfile for apache2whnc
# docker build --no-cache -t apache2whnc:latest -f Dockerfile .
# docker run -p 8080:80 --rm -it apache2whnc:latest bash
# docker build --no-cache -t git.netcologne.de:4567/oeberlein/apache2whnc -f Dockerfile .
#
#

FROM debian:jessie-slim

LABEL maintainer "webmaster@netcologne.de"

RUN echo "deb http://debian.netcologne.de/debian/ jessie main contrib non-free" > /etc/apt/sources.list
RUN echo "deb http://debian.netcologne.de/debian-security jessie/updates main contrib non-free" >> /etc/apt/sources.list

# install packages

RUN apt-get update

RUN apt-get install apt-utils aptitude -y locales net-tools procps git curl wget vim
RUN apt-get install -y apache2 libapache2-mod-fcgid apache2-suexec-custom
RUN apt-get install libnss-extrausers
RUN apt-get install -y graphicsmagick-imagemagick-compat

RUN rm -rf /var/lib/apt/lists/* 
RUN localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.utf8

######################### setup apache2 #########################

# apache2 Sites
RUN rm /etc/apache2/sites-enabled/*
ADD files/etc/apache2/sites-available /etc/apache2/sites-available
RUN /usr/sbin/a2ensite redirect-server-80
RUN /usr/sbin/a2ensite redirect-server-443
RUN /usr/sbin/a2ensite ssl.netcologne-hosting.de-443.conf

# apache conf
RUN rm /etc/apache2/conf-enabled/*
ADD files/etc/apache2/conf-available /etc/apache2/conf-available
RUN /usr/sbin/a2enconf apache2-doc
RUN /usr/sbin/a2enconf apache-logging
RUN /usr/sbin/a2enconf cgi
RUN /usr/sbin/a2enconf charset
RUN /usr/sbin/a2enconf javascript-common
RUN /usr/sbin/a2enconf letsencrypt
RUN /usr/sbin/a2enconf redirects
RUN /usr/sbin/a2enconf remoteip
RUN /usr/sbin/a2enconf security
RUN /usr/sbin/a2enconf servername
RUN /usr/sbin/a2enconf sperre


# apache modules
RUN /usr/sbin/a2dismod mpm_event
RUN /usr/sbin/a2enmod mpm_prefork
RUN /usr/sbin/a2enmod suexec

RUN /usr/sbin/a2enmod charset_lite
RUN /usr/sbin/a2enmod remoteip
RUN /usr/sbin/a2enmod rewrite
RUN /usr/sbin/a2enmod headers
RUN /usr/sbin/a2enmod ssl

RUN /usr/sbin/a2enmod fcgid
ADD files/etc/apache2/mods-available/fcgid.conf /etc/apache2/mods-available/fcgid.conf
ADD files/etc/apache2/mods-available/mpm_prefork.conf /etc/apache2/mods-available/mpm_prefork.conf

COPY files/etc/apache2/suexec/www-data /etc/apache2/suexec/www-data

RUN chmod 755 /var/log/apache2

RUN echo "IncludeOptional /etc/apache2/virtual-server-80-443/*.conf" >> /etc/apache2/apache2.conf

# libnss-extrausers
COPY files/etc/nsswitch.conf /etc/nsswitch.conf
COPY files/singlefiles/passwd /var/lib/extrausers/passwd
RUN groupadd -g 10000 www

# prevent Debian's PHP packages from being installed
# https://github.com/docker-library/php/pull/542
RUN set -eux; \
	{ \
		echo 'Package: php*'; \
		echo 'Pin: release *'; \
		echo 'Pin-Priority: -1'; \
	} > /etc/apt/preferences.d/no-debian-php

# dependencies required for running "phpize"
# (see persistent deps below)
ENV PHPIZE_DEPS \
		autoconf \
		dpkg-dev \
		file \
		g++ \
		gcc \
		libc-dev \
		make \
		pkg-config \
		re2c


# persistent / runtime deps
RUN apt-get update && apt-get install -y \
		$PHPIZE_DEPS \
		ca-certificates \
		curl \
		xz-utils \
        apt aptitude apt-utils \
	--no-install-recommends && rm -r /var/lib/apt/lists/*

########################### define php urls ###########################

ENV PHP_URL56="https://secure.php.net/get/php-5.6.40.tar.xz/from/this/mirror"
ENV PHP_URL71="https://secure.php.net/get/php-7.1.30.tar.xz/from/this/mirror"
ENV PHP_URL72="https://secure.php.net/get/php-7.2.20.tar.xz/from/this/mirror"
ENV PHP_URL73="https://secure.php.net/get/php-7.3.7.tar.xz/from/this/mirror"

########################### extract php source###########################

RUN set -eux; \
	mkdir -p /usr/src/php56; \
	cd /usr/src; \
	wget -O php56.tar.xz "$PHP_URL56"; \
    tar -Jxf /usr/src/php56.tar.xz -C /usr/src/php56 --strip-components=1

RUN set -eux; \
	mkdir -p /usr/src/php71; \
	cd /usr/src; \
	wget -O php71.tar.xz "$PHP_URL71"; \
    tar -Jxf /usr/src/php71.tar.xz -C /usr/src/php71 --strip-components=1

RUN set -eux; \
	mkdir -p /usr/src/php72; \
	cd /usr/src; \
	wget -O php72.tar.xz "$PHP_URL72"; \
    tar -Jxf /usr/src/php72.tar.xz -C /usr/src/php72 --strip-components=1

RUN set -eux; \
	mkdir -p /usr/src/php73; \
	cd /usr/src; \
	wget -O php73.tar.xz "$PHP_URL73"; \
    tar -Jxf /usr/src/php73.tar.xz -C /usr/src/php73 --strip-components=1

####################### install lib for php #######################

RUN set -eux; \
	apt-get update; \
	apt-get install -y \
		libcurl4-openssl-dev \
		libedit-dev \
		libsodium-dev \
		libsqlite3-dev \
		libssl-dev \
		libxml2-dev \
		zlib1g-dev \
        libzip-dev \
        libbz2-dev \
        libgdbm-dev \
        libgd3 \
        libgd2-xpm-dev \
        libgd-dev \
        libc-client2007e-dev \
        libkrb5-dev \
        libpq-dev \
        libpspell-dev \
        libtidy-dev \
        libxslt1-dev \
		libmcrypt-dev \
		icu-devtools libicu-dev


########################## compile php-5.6.x ##########################

RUN set -eux; \
	cd /usr/src/php56; \
	ls -la; \
	./configure \
		--prefix=/usr/local/php5.6 \
		--enable-bcmath  \
		--enable-calendar  \
		--enable-debug=no  \
		--enable-exif  \
		--enable-fpm  \
		--enable-ftp \
		--enable-intl \
		--enable-mbstring \
		--enable-shmop  \
		--enable-soap  \
		--enable-wddx  \
		--enable-zip  \
		--with-bz2  \
		--with-curl  \
		--with-gdbm  \
		--with-gettext  \
		--with-iconv  \
		--with-imap  \
		--with-imap-ssl  \
		--with-kerberos  \
		--with-libxml-dir  \
		--with-mcrypt  \
		--with-mhash  \
		--with-mysql \
		--with-mysqli \
		--enable-embedded-mysqli \
		--with-openssl  \
		--without-pear  \
		--with-pdo-mysql  \
		--with-png-dir  \
		--with-tidy  \
		--with-xsl  \
		--with-zlib \
		--with-gd \
		--with-vpx-dir \
		--with-jpeg-dir \
		--with-png-dir \
		--with-zlib-dir \
		--with-xpm-dir \
		--with-freetype-dir \
		--with-gettext \
		--with-mhash \
		--with-pdo-pgsql \
		--with-pspell \
		--with-libxml-dir \
		--with-tidy \
		--with-xmlrpc \
		--with-iconv-dir \
		--with-xsl \
		--with-zlib-dir \
		--with-pcre-dir \
	; \	
	make -j4; \
	make install; \
	make clean

########################## compile php-7.1.x ##########################

RUN set -eux; \
	cd /usr/src/php71; \
	ls -la; \
	./configure \
		--prefix=/usr/local/php7.1 \
		--enable-bcmath  \
		--enable-calendar  \
		--enable-debug=no  \
		--enable-exif  \
		--enable-ftp \
		--enable-intl \
		--enable-mbstring \
		--enable-shmop  \
		--enable-soap  \
		--enable-wddx  \
		--enable-zip  \
		--with-bz2  \
		--with-curl  \
		--with-gdbm  \
		--with-gettext  \
		--with-iconv  \
		--with-imap  \
		--with-imap-ssl  \
		--with-kerberos  \
		--with-libxml-dir=/usr  \
		--with-mcrypt  \
		--with-mhash  \
		--with-openssl  \
		--without-pear  \
		--with-png-dir=/usr  \
		--with-tidy  \
		--with-xsl  \
		--with-zlib \
		--with-gd \
		--with-jpeg-dir=/usr \
		--with-png-dir=/usr \
		--with-zlib-dir=/usr \
		--with-xpm-dir=/usr \
		--with-freetype-dir=/usr \
		--with-gettext \
		--with-mhash \
		--with-pdo-pgsql \
		--with-pspell \
		--with-libxml-dir=/usr \
		--enable-gd-native-ttf \
		--with-tidy \
		--with-xmlrpc \
		--with-iconv-dir=/usr \
		--with-xsl \
		--with-zlib-dir=/usr \
		--with-pcre-dir=/usr \
		--enable-mysqlnd --with-pdo-mysql --with-mysqli=mysqlnd \
		--disable-opcache \
		--with-pdo-mysql=mysqlnd \
	; \
	make -j4; \
	make install; \
	make clean
########################## compile php-7.2.x ##########################

RUN set -eux; \
	cd /usr/src/php72; \
	ls -la; \
	./configure \
		--prefix=/usr/local/php7.2 \
		--enable-bcmath  \
		--enable-calendar  \
		--enable-debug=no  \
		--enable-exif  \
		--enable-ftp \
		--enable-intl \
		--enable-mbstring \
		--enable-shmop  \
		--enable-soap  \
		--enable-wddx  \
		--enable-zip  \
		--with-bz2  \
		--with-curl  \
		--with-gdbm  \
		--with-gettext  \
		--with-iconv  \
		--with-imap  \
		--with-imap-ssl  \
		--with-kerberos  \
		--with-libxml-dir=/usr  \
		--with-mhash  \
		--with-openssl  \
		--without-pear  \
		--with-png-dir=/usr  \
		--with-tidy  \
		--with-xsl  \
		--with-zlib \
		--with-gd \
		--with-jpeg-dir=/usr \
		--with-png-dir=/usr \
		--with-zlib-dir=/usr \
		--with-xpm-dir=/usr \
		--with-freetype-dir=/usr \
		--with-gettext \
		--with-mhash \
		--with-pdo-pgsql \
		--with-pspell \
		--with-libxml-dir=/usr \
		--with-tidy \
		--with-xmlrpc \
		--with-iconv-dir=/usr \
		--with-xsl \
		--with-zlib-dir=/usr \
		--with-pcre-dir=/usr \
		--enable-mysqlnd --with-pdo-mysql --with-mysqli=mysqlnd \
		--disable-opcache \
		--with-pdo-mysql=mysqlnd \
	; \
	make -j4; \
	make install; \
	make clean

########################## compile php-7.3.x ##########################

RUN set -eux; \
	cd /usr/src/php73; \
	ls -la; \
	./configure \
		--prefix=/usr/local/php7.3 \
		--enable-bcmath  \
		--enable-calendar  \
		--enable-debug=no  \
		--enable-exif  \
		--enable-ftp \
		--enable-intl \
		--enable-mbstring \
		--enable-shmop  \
		--enable-soap  \
		--enable-wddx  \
		--enable-zip  \
		--with-bz2  \
		--with-curl  \
		--with-gdbm  \
		--with-gettext  \
		--with-iconv  \
		--with-imap  \
		--with-imap-ssl  \
		--with-kerberos  \
		--with-libxml-dir=/usr  \
		--with-mhash  \
		--with-openssl  \
		--without-pear  \
		--with-png-dir=/usr  \
		--with-tidy  \
		--with-xsl  \
		--with-zlib \
		--with-gd \
		--with-jpeg-dir=/usr \
		--with-png-dir=/usr \
		--with-zlib-dir=/usr \
		--with-xpm-dir=/usr \
		--with-freetype-dir=/usr \
		--with-gettext \
		--with-mhash \
		--with-pdo-pgsql \
		--with-pspell \
		--with-libxml-dir=/usr \
		--with-tidy \
		--with-xmlrpc \
		--with-iconv-dir=/usr \
		--with-xsl \
		--with-zlib-dir=/usr \
		--with-pcre-dir=/usr \
		--enable-mysqlnd --with-pdo-mysql --with-mysqli=mysqlnd \
		--disable-opcache \
		--with-pdo-mysql=mysqlnd \
	; \
	make -j4; \
	make install; \
	make clean

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
