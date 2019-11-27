# docker build --no-cache -t apache2debian:latest ./apache2


# Install Debian 9.x
FROM debian:stretch-slim
MAINTAINER Oliver Eberlein <oliver.eberlein@netcologne.de>
RUN echo "deb http://debian.netcologne.de/debian/ stretch main contrib non-free" > /etc/apt/sources.list
RUN echo "deb http://debian.netcologne.de/debian-security stretch/updates main contrib non-free" >> /etc/apt/sources.list

WORKDIR /apache

RUN apt-get update
RUN apt-get install apt-utils aptitude -y locales net-tools procps curl
RUN apt-get install -y git curl apache2
RUN rm -rf /var/lib/apt/lists/* 
RUN localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.utf8

RUN rm /etc/apache2/sites-enabled/*
RUN /usr/sbin/a2ensite 000-default

ADD conf-available /etc/apache2/conf-available
ADD sites-available /etc/apache2/sites-available

RUN /usr/sbin/a2enconf servername
RUN /usr/sbin/a2disconf other-vhosts-access-log

RUN /usr/sbin/a2enmod proxy
RUN /usr/sbin/a2enmod proxy_fcgi

COPY varwww/ /var/www/html/



EXPOSE 80


CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
