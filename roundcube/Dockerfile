# Dockerfile for roundcube
# docker build -t roundcube:latest -f Dockerfile .
# docker run -p 8080:80 --rm -it roundcube:latest bash
# docker build -t git.netcologne.de:4567/oeberlein/roundcube -f Dockerfile .
# --no-cache
# docker push git.netcologne.de:4567/oeberlein/roundcube:latest

 # docker build -t roundcube:latest -f Dockerfile .
# docker run -it --rm -v /home/oeberlein/Data/_Projects/docker/compose/roundcube/content:/var/www/html -p 8080:80 roundcube:latest bash
# wget https://github.com/roundcube/roundcubemail/releases/download/1.3.9/roundcubemail-1.3.9-complete.tar.gz
# tar xzvf roundcubemail-1.3.9-complete.tar.gz --strip-components=1

FROM php:7.3-apache

LABEL maintainer "webmaster@netcologne.de"

RUN echo "deb http://debian.netcologne.de/debian/ buster main contrib non-free" > /etc/apt/sources.list
RUN echo "deb http://debian.netcologne.de/debian-security buster/updates main contrib non-free" >> /etc/apt/sources.list

RUN apt-get update
RUN apt-get install -y wget locales
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
        libgdchart-gd2-xpm-dev \
        libgd-dev \
        libc-client2007e-dev \
        libkrb5-dev \
        libpq-dev \
        libpspell-dev \
        libtidy-dev \
        libxslt1-dev \
		libmcrypt-dev \
		icu-devtools libicu-dev \
        libmagickwand-dev \
        libldap2-dev

RUN rm -rf /var/lib/apt/lists/* 
RUN localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

RUN ln -fs /usr/share/zoneinfo/Europe/Berlin /etc/localtime
RUN dpkg-reconfigure -f noninteractive tzdata

ENV LANG en_US.utf8

RUN pecl install imagick
RUN docker-php-ext-enable imagick
RUN docker-php-ext-install intl exif ldap gd pdo_mysql

#ENV RC="https://github.com/roundcube/roundcubemail/releases/download/1.3.9/roundcubemail-1.3.9-complete.tar.gz"

#RUN set -eux; \
#    cd /var/www; \
#    wget -O rc.tar.gz "$RC"; \
#    tar -xzvf /var/www/rc.tar.gz -C /var/www/html --strip-components=1
#RUN chown 33:33 /var/www/html
#RUN rm /var/www/html/index.html
RUN rm -rf /var/lib/apt/lists/* 

RUN echo "date.timezone = \"Europe/Berlin\"" > /usr/local/etc/php/conf.d/timezone.ini

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]