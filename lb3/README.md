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

* Webseite aufrufen: http://localhost:8080/adminer.php
  Erfolgreich: Ja

* Anmelden auf Webseite.
  Erfolgreich: Nein

### Sicherheit

Syslog Protokollierung:

    $ docker run -d --log-driver=syslog ubuntu bash -c 'i=0; while true; do i=$((i+1)); echo "docker $i"; sleep 1; done;'
    $ tail -f /var/log/syslog


### Vorgehensweise

Wir haben einen MySQL-Container und WordPress-Container erstellt und miteinander verbunden.

$ docker run --name mysql-service -e MYSQL_ROOT_PASSWORD=root -d mysql
$ docker run --name my-wordpress \
   -e WORDPRESS_DB_PASSWORD=root \
   -e WORDPRESS_DB_HOST=mysql.my-wordpress \
   --link mysql-service:mysql.my-wordpress \
   -d wordpress

MySQL kann nun vom WordPress-Container aus mit dem Hostnamen mysql.my-wordpress erreicht werden.

Mit folgenden zwei Befehlen kann man die Netzwerkkarten genauer anschauen:

$ docker network inspect bridge

$ sudo iptables -L -n

$ docker network create wp-test
$ docker run --name mysql-service -e MYSQL_ROOT_PASSWORD=root --network
   wp-test -d mysql
$ docker run --name my-wordpress \
   -e WORDPRESS_DB_PASSWORD=root \
   --network wp-test \
   -d wordpress

### Aufgetretene Probleme

* Docker Image konnten nicht heruntergeladen werden: Error response from daemon: Get https://index.docker.io/v1/search?q=mysql&n=25: dial tcp: lookup index.docker.io on 10.0.2.3:53: read udp 10.0.2.15:38172->10.0.2.3:53: i/o timeout
  --> sudo nano /etc/resolvconf/resolv.conf.d/head
      nameserver 192.168.0.1 
      nameserver 0.0.0.0
      nameserver 8.8.8.8
      Speichern und verlassen
      sudo resolvconf -u
