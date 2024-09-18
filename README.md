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
[...]
Festplatte 50GB Eingebunden über Proxmox

Prüfen ob Festplatte erkannt wurde
```bash
sudo fdisk -l
```
Partition auf der Platte erstellen
```bash
sudo fdisk /dev/sdb
```
```
1. n
2. p
3. 1
4. 2x Enter
5. w
```
n (Partition erstellen)
p (Primäre Partition)
1 (Partition Nummer)
w (Partitionstabelle schreiben)

EXT4 Dateisystem anpassen
```bash
sudo mkfs.ext4 /dev/sdb1
```
rebooten

Mountpoint erstellen
```bash
sudo mkdir -p /mnt/austausch
```
Partition temporär mounten
```bash
sudo mount /dev/sdb1 /mnt/austausch
```
Beim booten mounten

fstab öffnen
```bash
sudo nano /etc/fstab
```

Zeile einfügen (nach format in der Config)
```bash
/dev/sdb1  /mnt/austausch  ext4  defaults  0  2
```

Daemons reloaden (weil sonst wird alte fstab genutzt)
```bash
systemctl daemon-reload
```
Überprüfen
```bash
sudo mount -a
```


