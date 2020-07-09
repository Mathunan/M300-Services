LB3
----------------------
	
### Beschreibung

* Web Server mit Apache und MySQL UserInterface [Adminer](https://www.adminer.org/)
* Datenbank Server mit MySQL

* Die Verbindung Web - Datenbank erfolgt mittels Internen Netzwerk Adapter.
* Von Aussen ist nur der HTTP Port auf dem Web Server Erreichbar.

Um in die VM zu wechseln ist zusätzlich der in Vagrantfile definierte Name einzugeben.

	vagrant ssh database
	vagrant ssh web

Das MySQL User Interface ist via [http://localhost:8080/adminer.php](http://localhost:8080/adminer.php) mit User/Password: root/admin erreichbar.

### Tests

* Webseite aufrufen: http://localhost:8080
  Erfolgreich: Nein


### Sicherheit

Syslog Protokollierung:

    $ docker run -d --log-driver=syslog ubuntu bash -c 'i=0; while true; do i=$((i+1)); echo "docker $i"; sleep 1; done;'
    $ tail -f /var/log/syslog


Überwachen und Benachrichtigen:

 * Wir haben folgenden Befehl eingegeben, um dann die Konfigurationen auf der Webseite vorzunehmen. Leider konnten wir die Webseite nicht öffnen und da wir schon unter Zeitdruck waren, konnten wir leider nicht weitermachen.

    $ docker run -d --name cadvisor -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys:ro -v /var/lib/docker/:/var/lib/docker:ro -p 8080:8080 google/cadvisor:latest


### Vorgehensweise

Container Verbinden

 * Wir haben einen MySQL-Container und WordPress-Container erstellt und miteinander verbunden.

	$ docker run --name mysql-service -e MYSQL_ROOT_PASSWORD=root -d mysql
	$ docker run --name my-wordpress \
	   -e WORDPRESS_DB_PASSWORD=root \
	   -e WORDPRESS_DB_HOST=mysql.my-wordpress \
	   --link mysql-service:mysql.my-wordpress \
	   -d wordpress

 * MySQL kann nun vom WordPress-Container aus mit dem Hostnamen mysql.my-wordpress erreicht werden.

 * Mit folgenden zwei Befehlen kann man die Netzwerkkarten genauer anschauen:

	$ docker network inspect bridge

	$ sudo iptables -L -n

	$ docker network create wp-test
	$ docker run --name mysql-service -e MYSQL_ROOT_PASSWORD=root --network
	   wp-test -d mysql
	$ docker run --name my-wordpress \
 	  -e WORDPRESS_DB_PASSWORD=root \
 	  --network wp-test \
 	  -d wordpress


Volumes

Zuerst haben wir einen Volume erstellt:

    $ docker volume create volumename

Danach habe wir es einem Container angehängt, welches erstellt werden sollte:

    $ docker run  -it --name c2 -v volumename:/var/lib/mysql --rm busybox

### Aufgetretene Probleme

* Docker Image konnten nicht heruntergeladen werden: Error response from daemon: Get https://index.docker.io/v1/search?q=mysql&n=25: dial tcp: lookup index.docker.io on 10.0.2.3:53: read udp 10.0.2.15:38172->10.0.2.3:53: i/o timeout
  --> 
	sudo nano /etc/resolvconf/resolv.conf.d/head
	nameserver 192.168.0.1 
	nameserver 0.0.0.0
	nameserver 8.8.8.8
Speichern und verlassen
	sudo resolvconf -u

### Befehle

|       Befehl            |          Was macht es?                     |
| :---------------------- | :------------------------------            |
| docker run hello-world  | Schauen, ob Docker korrekt funktioniert    |
| docker ps               | Aktive Container anzeigen                  |
| docker ps -a            | Alle Container anzeigen                    |
| docker images           | Lokale Images anzeigen                     |
| docker rm name          | Container löschen                          |
| docker create name      | Container erstellen                        |
| docker start id         | Container starten                          |
| docker stop             | Stoppt Container ohne sie zu entfernen     |
| docker kill             | Container wird sofort gestoppt             |
