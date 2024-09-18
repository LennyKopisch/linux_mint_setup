# Linux Mint Setup

<h3> 1.1 Updates </h3>

<b>System Updaten</b>
```bash
sudo apt-get update && sudo apt-get upgrade
```

<h3> 1.2 Benutzer </h3>

<b>Benutzer Anlegen (Benutzer01 & Benutzer02)</b>
```bash
sudo adduser Benutzer01 --allow-bad-names && sudo adduser Benutzer02 --allow-bad-names
```
[...]
Passwörter hinzufügen (automatisch bei adduser)

<b>Oder nachträglich ändern/setzen</b>
```bash
sudo passwd benutzername
```

<b>Gruppe mit Passwort erstellen</b>
```bash
sudo groupadd abdtest -p passwort
```

<b>Gruppe den Benutzern zuweisen</b>
```bash
sudo usermod -aG abtest Benutzer01 && sudo usermod -aG abtest Benutzer02
```

<b>Shell für Benutzer sperren</b>
```bash
sudo usermod -s /usr/sbin/nologin Benutzer02
```
Shell wieder freigeben
```bash
sudo usermod -s /bin/bash benutzername
```

<h3> 1.3 Mountpoint </h3>

```bash
sudo apt-get update && sudo apt-get upgrade
```
