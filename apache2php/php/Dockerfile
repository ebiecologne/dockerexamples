# Install Debian 9.x
FROM debian:stretch-slim
MAINTAINER Oliver Eberlein <oliver.eberlein@netcologne.de>
RUN echo "deb http://debian.netcologne.de/debian/ stretch main contrib non-free" > /etc/apt/sources.list
RUN echo "deb http://debian.netcologne.de/debian-security stretch/updates main contrib non-free" >> /etc/apt/sources.list

RUN apt-get update
RUN apt-get install apt-utils aptitude -y locales
RUN apt-get install -y git curl apache2
RUN rm -rf /var/lib/apt/lists/* 
RUN localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.utf8

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]