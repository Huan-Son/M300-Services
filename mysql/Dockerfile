FROM ubuntu:14.04

MAINTAINER Marc Mueller



#Prompt befehle für mysq-server

RUN echo 'mysql-server mysql-server/root_spassword password Password01' | debconf-set-selections 

RUN echo 'mysql-server mysql-server/root_password_again password Password01' | debconf-set-selections 



# Installation

RUN apt-get update

RUN apt-get install -y mysql-server


#Port für alle Hosts oeffnen

RUN sed -i -e"s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" /etc/mysql/my.cnf




EXPOSE 3306



VOLUME /var/lib/mysql



CMD ["mysqld"]