# Debian-Cheatsheet
Sammlung an Befehlen und Configs rund um Debian.

## Inhaltsverzeichniss
- [Direkt nach der Installation](#direkt-nach-der-installation)
  * [`$PATH` richtig definieren](#path-richtig-definieren)
  * [`sudo` erlauben](#sudo-erlauben)
  * [`swap` ausschalten](#swap-ausschalten)
  * [`GRUB` anpassen](#grub-anpassen)
  * [`sources.list` anpassen](#sourceslist-anpassen)
  * [`nala` installieren](#nala-installieren)
  * [`cups` ruhig stellen](#cups-ruhig-stellen)
  * [`.bashrc` anpassen](#bashrc-anpassen)
  * [`.bash_aliases` anlegen und befüllen](#bash_aliases-anlegen-und-befüllen)
  * [SSH-Login Scripte und Banner einstellen](#ssh-login-scripte-und-banner-einstellen)
- [Beim Cinnamon Desktop den letzen User anzeigen](#beim-cinnamon-desktop-den-letzen-user-anzeigen)
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

### `nala` installieren
Nala ist ein modernes `apt` Frontend .
```
sudo apt inntall nala -y
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

### `.bash_aliases` anlegen und befüllen
```
touch .bash_aliases
nano .bash_aliases
```
Folgende Befehle kann man dort eintragen:
```
### eigene Aliase mit Nala als APT Frontend
alias aktualisieren='sudo nala clean && sudo nala upgrade'
alias aufraeumen='sudo nala autopurge'
alias herunterfahren='aktualisieren && sudo poweroff'
alias neustarten='aktualisieren && sudo reboot'
alias wetter='curl de.wttr.in/roth?0'

### Original APT Befehle und Backups
#alias aktualisieren='sudo apt clean && sudo apt update && sudo apt full-upgrade -u'
#alias aufraeumen='sudo apt --purge autoremove'
```
Hier sind spezielle Aliase für den Raspi:
```
alias eeprom-updaten='sudo rpi-eeprom-update'
alias firmware-updaten='sudo PRUNE_MODULES=1 RPI_REBOOT=1 rpi-update'
alias padd='/home/pi/padd.sh --runonce'
alias uphole='sudo PIHOLE_SKIP_OS_CHECK=true pihole -up'

#alias padd='/home/pi/padd.sh --secret FILLSECRETHERE'
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
neofetch
└── Shows Linux System Information with Distribution Logo
fastfetch
└── neofetch-like tool for fetching system information
atop
└── Monitor for system resources and process activity
btop
└── Modern and colorful command line resource monitor that shows usage and stats
bashtop
└── Resource monitor that shows usage and stats
htop
└── interactive processes viewer
iftop
└── displays bandwidth usage information on an network interface
iotop
└── simple top-like I/O monitor
usbtop
└── utility to show bandwidth on USB buses and devices
bmon
└── portable bandwidth monitor and rate estimator
nmon
└── performance monitoring tool for Linux
net-tools
└── NET-3 networking toolkit
sysstat
└── system performance tools for Linux
iperf3
└── Internet Protocol bandwidth measuring tool
linux-perf
└── Performance analysis tools for Linux
hardinfo
└── transitional package [hardinfo2]
hardinfo2
└── System Information and Benchmark for Linux Systems
sl
└── Correct you if you type `sl' by mistake
```
