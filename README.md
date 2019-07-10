# Serverdienste automatisieren mit Vagrant

## Inhaltsverzeichnis
- [Serverdienste automatisieren mit Vagrant](#serverdienste-automatisieren-mit-vagrant)
  - [Inhaltsverzeichnis](#inhaltsverzeichnis)
  - [Kurzbeschrieb](#kurzbeschrieb)
  - [Dockerbefehle](#dockerbefehle)
  - [Wissensstand vor der LB2](#wissensstand-vor-der-lb2)
  - [Implementierung](#implementierung)
    - [Containerplan](#containerplan)
    - [Dockerfile](#dockerfile)
- [Konfiguration Apache](#konfiguration-apache)
- [Installation](#installation)
  - [Testing](#testing)
  - [Troubleshooting](#troubleshooting)
    - [Allgemeines Troubleshooting](#allgemeines-troubleshooting)
    - [Ressourcen-Begrenzung](#ressourcen-begrenzung)
  - [Jetziger Wissensstand](#jetziger-wissensstand)
  - [Reflexion](#reflexion)

## Kurzbeschrieb
Mein Ziel war es die erstellung eines Containers für die MYSQL-Datenbank, sowie für einen Webserver mit phpMyAdmin zu Automatisieren.

## Dockerbefehle

|Befehl | Effekt|
|:--:|:--:|
|docker build|Aus einem Dockerfile heraus ein Image bauen|
|docker run|Container starten|
|docker start|Einen gestoppten Container starten|
|docker stop|Einen Container stoppen|
|docker kill|Einen Container gewaltsam stoppen|
|docker ps|Alle laufenden Container auflisten|
|docker compose|Multi-container Umgebung starten|

## Wissensstand vor der LB2
*Docker* </br>
Vor der LB hatte ich noch nie mit Docker gearbeitet.</br>

## Implementierung

### Containerplan
![Image](/Containerplan.PNG)

### Dockerfile

Die einzelnen Code Abschnitte des Dockerfiles.

Docker installieren
~~~~
sudo apt-get install docker.io
~~~~
Um Container 1 zu starten geht man in den Ordner webserver rein. Dort befindet sich ein Dockerfile. In diesem Ordner gibt man dann diese Befehle ein:
~~~~
docker build -t webserver .
docker run --rm -d -p 8080:80 webserver
~~~~
So sieht das Dockerfile aus:
~~~~
FROM ubuntu:14.04

MAINTAINER Marc Mueller



RUN apt-get update

RUN apt-get -q -y install apache2 


# Konfiguration Apache

ENV APACHE_RUN_USER www-data

ENV APACHE_RUN_GROUP www-data

ENV APACHE_LOG_DIR /var/log/apache2


RUN mkdir -p /var/lock/apache2 /var/run/apache2


#Für phpMyAdmin prompt-Config, das kommentierte sind die alten Befehle, welche ich umschreiben musste

#RUN debconf-set-selections <<< "phpmyadmin phpmyadmin/dbconfig-install boolean true"

#RUN debconf-set-selections <<< "phpmyadmin phpmyadmin/app-password-confirm password Password01"

#RUN debconf-set-selections <<< "phpmyadmin phpmyadmin/mysql/admin-pass password Password01"

#RUN debconf-set-selections <<< "phpmyadmin phpmyadmin/mysql/app-pass password Password01"

#RUN debconf-set-selections <<< "phpmyadmin phpmyadmin/reconfigure-webserver multiselect apache2"

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

~~~~
Als nächstes  in den Ordner mysql gehen und dort diese Befehle eingeben:
~~~~
docker build -t mysql .
docker run --rm -d -p 3306:3306 mysql
~~~~
So sieht dieses Dockerfile aus:
~~~~
FROM ubuntu:14.04

MAINTAINER Marc Mueller



#Prompt befehle für mysq-server

RUN echo 'mysql-server mysql-server/root_spassword password Password01' | debconf-set-selections 

RUN echo 'mysql-server mysql-server/root_password_again password Password01' | debconf-set-selections 



# Installation

RUN apt-get update

RUN apt-get install -y mysql-server


#Port für alle Hosts öffnen

RUN sed -i -e"s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" /etc/mysql/my.cnf

EXPOSE 3306


VOLUME /var/lib/mysql

CMD ["mysqld"]
~~~~

## Testing

|Testfall | Erwartet | Resultat |
|:--:|:--:|:--:|
|Webserver|Webserver kann im Browser abgerufen werden|Der Webserver konnte im Browser abgerufen werden|
|phpMyAdmin|Das phpMyAdmin Tool wird angezeigt|Das phpMyAdmin Tool wurde auf dem Webserver angezeigt|
|mySQL-Server|phpMyAdmin sollte mit dem mySQL-Server kommunizieren|Ich konnte über phpMyAdmin auf die MySQL-Datenbank zugreifen|

*phpMyAdminWebsite-Login* </br>
![Image](/phpmyadmin.png) </br>
*phpMyAdminWebsite* </br>
![Image](/login.png)

## Troubleshooting
### Allgemeines Troubleshooting
* Sicherstellen, dass man Root-Rechte hat.
* Schreib- und Leserechte sollten richtig verteilt sein.
* Das Mounten von Volumes braucht Shared Drives für Linux-Container.
* Auf die Versionen achten. Manche Versionen sind nicht miteinander kompatibel.

### Ressourcen-Begrenzung
|Suffix | Effekt|
|:--:|:--:|
|-m|RAM begrenzen. (-m 4m ist z.B. 4MB)|
|--kernel-memory|Kernel-Memory begrenzen|
|--cpus= value |Stellt ein wie viel CPU-Ressourcen der Container benutzen darf (z.B bei 2 CPUs kann man 1.5 eingeben um nicht alles zu benutzen|

## Jetziger Wissensstand
*Docker* </br>
Ich habe einiges neues über Docker dazugelernt.</br>

## Reflexion
Ich hatte mir Docker deutlich einfacher vorgestellt als es dann wirklich war.
Docker hat kürzere Code-Längen, verglichen mit Vagrant. Es war sehr ansprucksvoll und zeitaufwendig.