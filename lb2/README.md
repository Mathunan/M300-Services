Multi Machine Beispiel
----------------------

### Übersicht 

    +------------------------------------------------------------------+
    ! Notebook - Heimnetz 192.168.1.1 und Privates Netz 192.168.55.1   !                 
    ! Port: 8080 (192.158.55.101:80)                                   !	
    !                                                                  !	
    !    +--------------------+          +---------------------+       !
    !    ! Web Server         !          ! Datenbank Server    !       !       
    !    ! Host: web01        !          ! Host: db01          !       !
    !    ! IP: 192.168.55.101 ! <------> ! IP: 192.168.55.100  !       !
    !    ! Port: 80           !          ! Port 3306           !       !
    !    ! Nat: 8080          !          ! Nat: -              !       !
    !    +--------------------+          +---------------------+       !
    !                                                                  !  	
    +------------------------------------------------------------------+
	
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

* Datenbank Server bzw. MySQL ist mit Password geschützt.
* Der Web Server ist offen und mittels ungeschütztem HTTP Protokoll erreichbar.


### Vorgehensweise

Zuerst habe einen neuen Ordner in meinem Repository erstellt und dann alle nötigen Files für das Projekt vom Vagrant Ordner hineinkopiert. Danach habe ich die VMs mit

	vagrant up

gestartet. Beim ersten Mal wurde es zuerst noch aufgesetzt.

### Aufgetretene Probleme

Am Anfang hat es die eine VM nicht aufgesetzt. Nachdem ich im Internet nach möglichen Lösungen gesucht habe und auf eines gestossen bin, habe ich die VMs gelöscht und dann nochmals neu aufgesetzt:

	vagrant halt -f
	vagrant destroy -f

Leider hat es dann immernoch nicht funktioniert. Nachdem ich es mit Herr Calisto nochmals probiert habe, hat es auf einmal geklappt.