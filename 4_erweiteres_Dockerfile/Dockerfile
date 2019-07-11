# Dockerfile for apache2whnc
 
FROM debian:stretch-slim
 
LABEL maintainer "oliver.eberlein@netcologne.de"
 
RUN echo "deb http://debian.netcologne.de/debian/ stretch main contrib non-free" > /etc/apt/sources.list
RUN echo "deb http://debian.netcologne.de/debian-security stretch/updates main contrib non-free" >> /etc/apt/sources.list
 
# install packages
 
RUN apt-get update
 
RUN apt-get install apt-utils aptitude -y locales net-tools procps git curl wget vim
RUN apt-get install -y apache2 libapache2-mod-fcgid apache2-suexec-custom
RUN apt-get install libnss-extrausers
 
RUN rm -rf /var/lib/apt/lists/* 
RUN localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.utf8
 
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
