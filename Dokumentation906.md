# DevOPs Wahlmodul 906

## Inhaltsverzeichnis

- Planung
- Versionsverwaltung
- Codebetrieb
- Automatisierte Builds
- Kontinuierliche Integration
- Delivery


## 	Planung
Für die Planung verwenden wir Github.

[Bild 1]: Bilder/planung.jpg  "Planung"


## Inbetriebnahme des Containers

Diese Inbetriebnahme wurde getestet in der folgenden Umgebung: Linux Ubuntu Installation mit Docker.
Für das herunterladen des Git kann entweder SCP, RSYNC, oder FTP verwendet werden.

Um in das Verzeichnis zu wechseln führt man folgenden Befehl aus:
```
$ cd /apachecontainer
```
> **Hinweis:** Der Pfad muss auf die jeweilige Umgebung angepasst werden

Sobald man sich im entsprechendem Verzeichnis befindet kann man mit folgendem Befehl
einen Container mithilfe des Dockerfiles erstellen.		
```
$ docker build -t apache . 
```
Um den soeben erstellen Container zu starten braucht man folgenden Befehl:
```
$ docker run --rm -d -p 8080:80 -v `pwd`/web:/var/www/html --name apache apache
```
Der Befehl lässt den Apache Webserver starten mit der Portangabe 8080 und der Verzeichnisangabe /var/www/html unter dem Namen Apache.
Die Website ist nun unter der IP Adresse der VM, mit der Angabge des Ports: 8080, erreichbar.
Wenn dies nicht der Fall ist, dann schliesst dies auf einen Fehler in der Netzwerkkonfiguration der entsprechenden VM.

## Dockerfile
### Allgemeines zur Funktionsweise eines Dockerfiles
Docker baut Images automatisch auf mithilfe einer Dockerdatei. In einer Dockerdatei befinden sich die Anweisungen die Docker ausführen soll.
Diese Dockerdatei ist nichts anderes als eine ganz normale Textdatei welche alle Befehle für den Aufbau eines bestimmten Images, der Reihenfolge nach enthält.
Ein Dockerfile wird immer in einem bestimmten Format gehalten und mit einem bestimmten Satz von Anweisungen welche benötigt werden.
#
Ein Docker Image besteht aus vielen read-only Schichten. Jede dieser Schichten stellt eine Dockerfile Anweisung/Befehl dar.
Diese Schichten sind aufeinander gestapelt. Jede Schicht ist eine Reaktion auf die Änderungen in der vorherigen Schicht.

Mehr dazu in der nachfolgenden Doku über mein ausgewähltes Dockerfile.
#
#
```
FROM ubuntu:14.04
```
Mit FROM wird das Basisimage angegeben, welches die Grundlage für das neue zu erstellende Image bildet.
#
```
MAINTAINER Marcel mc-b Bernet <marcel.bernet@ch-open.ch>
```
Mit MAINTAINER wird ein Autor für das Image angegeben.
#
```
RUN apt-get update
```
Mithilfe des Befehls Der Befehl `RUN` wird apt-get update nun als Shell-Befehl im Guest OS ausgeführt.
Mit apt-get update werden die Pakete unter Linux kontrolliert und gegebenenfalls geupdatet.
#
```
RUN apt-get -q -y install apache2
```
Mit diesem Shell Befehl wird der Apache Webserver installiert.
Mit der Option `-q` welche für quiet steht wird der Output während der Installation geloggt. 
Mit der Option `-y` werden alle Aufforderunge an den auszuführenden User während der Installation automatisch bestätigt.
#
```
ENV APACHE_RUN_USER www-data
```
Mithilfe des Befehls `ENV` wird einer Environment Variabel den folgenden Wert mitgegeben.
In diesem Befehl wird die Environment Variabel APACHE_RUN_USER auf www-data gesetzt.
In diesem Fall wird der Apache User auf `www-data` gesetzt. 
#
```
ENV APACHE_RUN_GROUP www-data
```
Gleich wird hier die Environment Variabel Apache_Run_Group auf www-data gesetzt. 
#
```
ENV APACHE_LOG_DIR /var/log/apache2
```
Mit dieser Environment Variabel wird angegeben wo sich das Verzeichnis mit allen Logs befinden sollte.
Nämlich unter /var/log/apache2
#
```
RUN mkdir -p /var/lock/apache2 /var/run/apache2
```
Mit dem Befehl mkdir werden Verzeichnise erstellt. In diesem Befehl werden zwei Verzeichnise erstellt für Apache.
Mit der Option -p werden eventuell fehlende höhere Verzeichnisse miterstellt. 
```
EXPOSE 80
```
Mihilfe des Befehl `EXPOSE` kann man angeben welchen Port man auf dem Container öffnen möchte.
Es wurde der Port 80 geöffnet.
```
VOLUME /var/www/html
```
Mit `VOLUME` werden Mountpoints für externe Volumes erstellt.
Mit diesem Befehl wurde ein Mountpoint unter  `/var/www/html` erstellt. 
```
CMD /bin/bash -c "source /etc/apache2/envvars && exec /usr/sbin/apache2 -DFOREGROUND"
```
`CMD` definiert die Standards für einen Container unter Docker. Die Standard können Scripte beinhalten. 
In diesem Dockerfile wurde ein Script hinterlegt, welche den Webserver automatisch nach Containerstart startet.
 
## Testprotokoll
Ich habe alle folgenden Tests auf meinem System durchgeführt. Diese können auch als Kontrolle verwendet werden, ob der Container wie gewollt funktioniert. 
|Nr.|   Test        |Erwartetes Resultat            |Effektives Resultat          |Entscheid|
|---|---------------|-------------------------------|-----------------------------|-----------------|
|1  |Zugriff auf Webseite von Host OS Windows mit Browser URL: localhost:8080 |Anzeigen des index.html Files|Fehlermeldung Seite kann nicht gefunden werden| Nicht erfüllt, da die Website ja nicht auf dem Host OS Windows läuft kann auch nicht per localhost darauf zugegrifen werden.                       
|2  |Von Docker aus mithilfe von Curl auf die Webseite zugreifen.|Der Inhalt des index.html Files wird angezeigt|Das index.html File wird dargestellt |Erfüllt
|3  |Zugriff auf Webseite von Windows Host im Browser URL: ipadresse-container:8080|Der Inhalt des index.html Files wird angezeigt|Das index.html File wird dargestellt |Erfüllt

## Quellenangaben
**Quelle 1**
Offizielle Docker Dokumentation 
[https://docs.docker.com/)

**Quelle 2**
Docker einfach erklärt
[https://www.freecodecamp.org/news/docker-simplified-96639a35ff36/)

```
title: Dokumentation eines Dockerfiles LB3 Modul 300
author: Marc De Pra
date: 07.05.2020
```



