# Linux Mint Setup

<h3> 1.1 Updates </h3>

<b>System Updaten</b>
```bash
sudo apt-get update && sudo apt-get upgrade
```
__________________________________________________________________________________________________
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
__________________________________________________________________________________________________
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
__________________________________________________________________________________________________
<h3> 1.4 Remotezugriff </h3>
RDP Protokoll installieren 

```bash
sudo apt install xrdp
```
Daemon beim Boot starten, Daemon starten & prüfen
```bash
sudo systemctl enable xrdp
sudo systemctl start xrdp
sudo systemctl status xrdp
```

> [!NOTE]  
> OPTIONAL:
> 
> Für Mint optional XFce Desktop für Kompatibilität installieren: 
> ```bash
> sudo apt install xfce4
> ```
> Festlegen das XFce bei rdp genutzt wird, (~/.xsession befindet sich meist im Homeverzeichnis)
> ```bash
> echo xfce4-session >~/.xsession
> ```

Lokale Firewall RDP Port freigeben
```bash
sudo ufw allow 3389
```
> [!CAUTION]  
> Fehlerbehebung:
> RDP Client schließt nach login
> 
> Config aufmachen
> ```bash
> sudo nano /etc/xrdp/startwm.sh
> ```
> Desktopumgebung hinzufügen
> ```bash
> startxfce4
> ```
> Speichern und Daemon neu starten
> ```bash
> sudo systemctl restart xrdp
> ```

> [!TIP]
> RDP Verbindung Erfolgreich <br>
![✔️](https://img.shields.io/badge/-Fehler%20gefixt-brightgreen)


Team Viewer installieren

Über Bash:
```bash
wget https://download.teamviewer.com/download/linux/teamviewer_amd64.deb
```
Installieren
```bash
sudo dpkg -i teamviewer_amd64.deb
```
> [!NOTE]  
> Falls beschädigt
> ```bash
> sudo apt --fix-broken install
> ```
Teamviewer starten
```bash
teamviewer
```

VNC Server installieren

System updaten
```bash
sudo apt update
sudo apt upgrade
```
VNC-Server installieren
```bash
sudo apt install tigervnc-standalone-server tigervnc-common
```
Passwort setzen
```bash
vncpasswd passwort
```
Server starten
```bash
vncserver
```
> [!CAUTION]  
> Fehlerbehebung: <br>
> Server lehnt die Verbindun ab / Crasht beim Starten durch fehlerhafte Dateien etc.
> 
> Startconfig erstellen, bearbeiten und Ausführungsrechte geben:
> ```bash
> touch ~/.vnc/xstartup
> ```
> ```bash
> nano ~/.vnc/xstartup
> ```
> /usr/bin/cinnamon-session NUR FÜR MINT:
> ```bash
> #!/bin/sh
> unset SESSION_MANAGER
> unset DBUS_SESSION_BUS_ADDRESS  
> /usr/bin/cinnamon-session
> ```
> ```bash
> chmod +x ~/.vnc/xstartup
> ```
> 
> Firewall Port für VNC freigeben:
> ```bash
> sudo ufw allow 5901 
> ```
> Server starten ( -localhost no = Erlaube Verbindungen || :1 = Display Nummer zum Port 5901)
> ```bash
> vncserver -localhost no :1
> ```

> [!TIP]
> VNC Verbindung Erfolgreich <br>
![✔️](https://img.shields.io/badge/-Fehler%20gefixt-brightgreen)


SSH Zugriff

SSH installieren
```bash
sudo apt update
sudo apt install openssh-server
```
Config prüfen ob x11 forwarding gesetzt ist (Falls nicht Änderungen machen und Service neu starten)
```bash
sudo nano /etc/ssh/sshd_config
```
In meinem Fall war DisplayOffset auskommtiert, also habe ich das '#' entfernt
```bash
X11Forwarding yes
X11DisplayOffset 10
```
Service neu starten wie oben angesprochen
```bash
sudo systemctl restart ssh
```
X11 Packages installieren
```bash
sudo apt install xauth x11-apps
```
X11 Port freigeben:
```bash
sudo ufw allow 6000
```

PUTTY Installieren
PUTTY Einstellungen für X11 
> PuTTY öfnnen 
> Connection > SSH > X11
> Enable X11 forwarding“

VcXsrv Installieren
Starten

Fertig
> X display location -> localhost:0.0
__________________________________________________________________________________________________
<h3> 1.5 FTP Server </h3>

vsftpd installieren
```bash
sudo apt update
sudo apt install vsftpd
```

Verzeichnis erstellen und config machen
```bash
sudo mkdir -p /mnt/FTP
```
```bash
sudo chown ftp:ftp /mnt/FTP
sudo chmod 755 /mnt/FTP
```

vsftpd config
```bash
sudo nano /etc/vsftpd.conf
```
anonymous_enable=NO
local_enable=YES
write_enable=YES
> [!TIP]
> Wichtig damit der richtige Pfad für FTP genutzt wird: <br>
> local_root=/mnt/FTP <br>
> chroot_local_user=YES

Services starten und autostarten
```bash
sudo systemctl restart vsftpd
```

Firwall Port für FTP freimachen via TCP
```bash
sudo ufw allow 21/TCP
```

[...]
__________________________________________________________________________________________________
<h3> 1.6 AD Join </h3>

Pakete Installiert
```bash
sudo apt update
sudo apt install realmd sssd adcli krb5-user samba-common-bin packagekit
```

> DNS Server angepasst 

Active Directory mit realmd erkennen
```bash
realm discover lko-data.intern
```

Domäne joinen
```bash
sudo realm join --user=Administrator lko-data.intern
```

Konfiguration von pam.d
```bash
nano /etc/pam.d/common-session
session required pam_mkhomedir.so skel=/etc/skel/ umask=077
```

> Reboot

Anmeldung / Testen
```bash
Administrator@lko-data.intern
```
__________________________________________________________________________________________________

<h3> 1.7 Weiteres </h3>

Automatische Mail beim herunterfahren:
SMTP & mit Internet auswählen + Lokal
```bash
sudo apt install mailutils
```

Skript erstellen was die Mail schickt:
```bash
sudo nano /usr/local/bin/send-reboot-email.sh
```
```bash
#!/bin/bash

echo "Der Server wurde neu gestartet!" | mail -s "Server-Neustart" mail@ab-data.de
```
Rechte geben
```bash
sudo chmod +x /usr/local/bin/send-reboot-email.sh
```
Systemmd Dienst erstellen
```bash
sudo nano /etc/systemd/system/send-reboot-email.service
```
```bash
[Unit]
Description=Send reboot notification email
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/send-reboot-email.sh

[Install]
WantedBy=multi-user.target
```
Dienst aktivieren & Testen:
```bash
sudo systemctl enable send-reboot-email.service
```
```bash
sudo systemctl start send-reboot-email.service
```

Shutdown File

Skript erstellen
```bash
sudo nano /usr/local/bin/check-shutdown-file.sh
```bash
```
Inhalt
```bash
#!/bin/bash

FILE="/mnt/austausch/shutdown.txt"

if [ -f "$FILE" ]; then
    echo "Datei $FILE existiert. Server wird neu gestartet."
    rm -f "$FILE"
    sudo reboot
else
    echo "Datei $FILE existiert nicht. Kein Neustart erforderlich."
fi
```
Rechte verteilen
```bash
sudo chmod +x /usr/local/bin/check-shutdown-file.sh
```
Systemd-Timer erstellen
```bash
sudo nano /etc/systemd/system/check-shutdown-file.service
```
Inhalt
```bash
[Unit]
Description=Check shutdown.txt

[Service]
Type=oneshot
ExecStart=/usr/local/bin/check-shutdown-file.sh
```
Timer erstellen
```bash
sudo nano /etc/systemd/system/check-shutdown-file.timer
```
Inhalt
```bash
[Unit]
Description=check-shutdown-file alle 5minutos

[Timer]
OnBootSec=1min
OnUnitActiveSec=5min
Unit=check-shutdown-file.service

[Install]
WantedBy=multi-user.target
```
Timer aktivieren
```bash
sudo systemctl enable check-shutdown-file.timer
sudo systemctl start check-shutdown-file.timer
```
Testen
```bash
sudo systemctl start check-shutdown-file.service
```

gedit Öffnen wenn editor in bash geschrieben wird:

gedit installieren weil nicht standartmäßig auf mint installiert
```bash
sudo apt install gedit
```

Shell Config anpassen
```bash
nano ~/.bashrc
```
Einfügen:
```bash
alias editor='gedit'
```
Änderungen laden
```bash
source ~/.bashrc
```
__________________________________________________________________________________________________

<h3> 1.8 Fragen </h3>

# Was ist Swap?
> Swap ist ein Speicherbereich auf der Festplatte, der verwendet wird, wenn der physische RAM voll ist. Es gibt zwei Haupttypen: Swap-Partitionen und Swap-Dateien. Swap ermöglicht es dem System, mehr Daten zu verarbeiten, als im RAM vorhanden ist, aber ist langsamer als RAM.

# Was ist der Unterschied zwischen KDE, Gnome und Xfce?
> KDE (K Desktop Environment) bietet eine reichhaltige, anpassbare Benutzeroberfläche mit vielen Funktionen und Effekten. GNOME ist bekannt für sein einfaches, modernes Design und eine benutzerfreundliche, konsistente Oberfläche. Xfce ist leichtgewichtig und schnell, ideal für ältere Hardware oder Systeme mit begrenzten Ressourcen, und legt Wert auf Einfachheit und Stabilität.

# Mountpoint
> Siehe oben

# Unterschiede zwischen den Paketmanagern apt-get, rpm, yum und zypper?
> apt-get ist der Paketmanager für Debian-basierte Distributionen wie Ubuntu und verwendet .deb-Pakete. rpm ist der Paketmanager für Red Hat-basierte Distributionen und verwendet .rpm-Pakete. yum (Yellowdog Updater, Modified) verwaltet RPM-Pakete für Red Hat-basierte Distributionen wie CentOS und Fedora, bietet Abhängigkeitsauflösung und Repository-Verwaltung. zypper ist der Paketmanager für openSUSE und verwendet ebenfalls .rpm-Pakete, bietet aber zusätzliche Funktionen wie Abhängigkeitsmanagement und Repositories.
__________________________________________________________________________________________________

Lenny Kopisch, 18.09.2024
