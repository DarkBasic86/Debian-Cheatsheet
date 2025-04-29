# Debian-Cheatsheet
Sammlung an Befehlen und Configs rund um Debian.

## Inhaltsverzeichniss
- [Direkt nach der Installation](#direkt-nach-der-installation)
  * [`$PATH` richtig definieren](#path-richtig-definieren)
  * [`sudo` erlauben](#sudo-erlauben)
  * [`swap` ausschalten](#swap-ausschalten)
  * [`GRUB` anpassen](#grub-anpassen)
  * [`sources.list` anpassen](#sourceslist-anpassen)
  * [`cups` ruhig stellen](#cups-ruhig-stellen)
  * [`.bashrc` anpassen](#bashrc-anpassen)
  * [SSH-Login Scripte und Banner einstellen](#ssh-login-scripte-und-banner-einstellen)
- [eim Cinnamon Desktop den letzen User anzeigen](#beim-cinnamon-desktop-den-letzen-user-anzeigen)
- [Sinnvolle Pakete](#sinnvolle-pakete)

## Direkt nach der Installation
Mit `su` in den `root` User wechseln:

### `$PATH` richtig definieren
```
nano /etc/profile
```
```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games"
```

### `sudo` erlauben
Mit `visudo` den User in die `sudeors` eintragen:
```
darky  ALL=(ALL:ALL) NOPASSWD: ALL
```
Der Parameter `NOPASSWD:` ermöglicht es, dass dieser User nicht jede Sitzung sein Passwort eingeben muss.

### `swap` ausschalten
```
sudo swapoff -a
```

### `GRUB` anpassen
```
sudo nano /etc/default/grub
```
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash net.ifnames=0 biosdevname=0 ipv6.disable=1 systemd.swap=0"
```
und noch den DISABLE_OS_PROBER einkommentieren und auf `true` setzen
```
GRUB_DISABLE_OS_PROBER=true
```
```
sudo update-grub && sudo reboot
```

### `sources.list` anpassen
```
sudo nano /etc/apt/sources.list
```
```
deb http://deb.debian.org/debian/ trixie main contrib non-free non-free-firmware
deb http://security.debian.org/debian/ trixie-security main contrib non-free non-free-firmware
deb http://deb.debian.org/debian/ trixie-updates main contrib non-free non-free-firmware
deb http://deb.debian.org/debian/ trixie-proposed-updates main contrib non-free non-free-firmware
deb http://deb.debian.org/debian/ trixie-backports main contrib non-free non-free-firmware

#deb-src http://deb.debian.org/debian/ trixie main contrib non-free non-free-firmware
#deb-src http://security.debian.org/debian/ trixie-security main contrib non-free non-free-firmware
#deb-src http://deb.debian.org/debian/ trixie-updates main contrib non-free non-free-firmware
#deb-src http://deb.debian.org/debian/ trixie-proposed-updates main contrib non-free non-free-firmware
#deb-src http://deb.debian.org/debian/ trixie-backports main contrib non-free non-free-firmware
```

Eventluell noch die `i386` Pakete hinzufügen
```
sudo dpkg --add-architecture i386
```

Man kann auch checken, welche Haupt und Zusätliche Architekturen isntalliert sind.
```
dpkg --print-architecture
```
```
dpkg --print-foreign-architecture
```

Einmal alles durchstarten
```
sudo apt clean
```
```
sudo apt update
```
```
sudo apt full-upgrade
```
```
sudo apt --purge autoremove
```

### `cups` ruhig stellen
```
sudo systemctl stop cups
sudo systemctl stop cups-browsed
```
```
sudo systemctl disable cups
sudo systemctl disable cups-browsed
```

### `.bashrc` anpassen
```
nano .bashrc
```
```
alias aktualisieren="sudo apt clean && sudo apt update && sudo apt full-upgrade && sudo apt --purge autoremove"
```

### SSH-Login Scripte und Banner einstellen
Den LEGAL Text während einer SSH-Verbindung deaktivieren:
```
sudo nano /etc/pam.d/sshd
```
Folgende Zeile auskommentieren:
```
#session     optional     pam_motd.so noupdate
```

Um weitere Zeilen dynamisch zu generieren, muss ein zusätzliches Script angelegt werden. Beispiel:
```
sudo nano /etc/update_motd.d/20-neofetch
```
Mit folgendem Inhalt wie ein sh Script:
```
#!/bin/sh
neofetch
```
Sie werden in numerisch aufsteigender Reihenfolge ausgeführt.

Zum Schluss das Script noch ausführbar machen:
```
sudo chmod +x /etc/update-motd.d/20-neofetch
```

## Beim Cinnamon Desktop den letzen User anzeigen
Mit diesem Befehl checken, ob wirklich der LightDM benutzt wird.
```
cat /etc/X11/default-display-manager
```

Folgende Zeile in der `/etc/lightdm/lightdm.conf` auskommentieren:
```
greeter-hide-users=false
```


## Sinnvolle Pakete
```
sudo apt install neofetch atop btop htop bmon net-tools hardinfo hardinfo2 sl
```
