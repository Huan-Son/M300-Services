FROM ubuntu:14.04

MAINTAINER Marc Mueller



RUN apt-get update

RUN apt-get -q -y install apache2 


# Konfiguration Apache

ENV APACHE_RUN_USER www-data

ENV APACHE_RUN_GROUP www-data

ENV APACHE_LOG_DIR /var/log/apache2


RUN mkdir -p /var/lock/apache2 /var/run/apache2


RUN echo 'phpmyadmin phpmyadmin/dbconfig-install boolean true' | debconf-set-selections

RUN echo 'phpmyadmin phpmyadmin/app-password-confirm password Password01' | debconf-set-selections

RUN echo 'phpmyadmin phpmyadmin/mysql/admin-pass password Password01' | debconf-set-selections

RUN echo 'phpmyadmin phpmyadmin/mysql/app-pass password Password01' | debconf-set-selections

RUN echo 'phpmyadmin phpmyadmin/reconfigure-webserver multiselect apache2' | debconf-set-selections


#Installation phpMyAdmin

RUN sudo apt-get -y install phpmyadmin


EXPOSE 80



VOLUME /var/www/html


CMD /bin/bash -c "source /etc/apache2/envvars && exec /usr/sbin/apache2 -DFOREGROUND"
