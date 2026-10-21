# Debian-Cheatsheet
Sammlung an Befehlen und Configs rund um Debian.

## Inhaltsverzeichnis
- [Direkt nach der Installation](#direkt-nach-der-installation)
  * [`$PATH` richtig definieren](#path-richtig-definieren)
  * [`sudo` erlauben](#sudo-erlauben)
  * [`swap` ausschalten](#swap-ausschalten)
  * [`GRUB` anpassen](#grub-anpassen)
  * [`sources.list` anpassen](#sourceslist-anpassen)
  * [`apt` modernisieren](#apt-modernisieren)
  * [`nala` installieren](#nala-installieren)
  * [`cups` ruhigstellen](#cups-ruhigstellen)
  * [`.bashrc` anpassen](#bashrc-anpassen)
  * [`.bash_aliases` anlegen und befüllen](#bash_aliases-anlegen-und-befüllen)
  * [SSH-Login Scripte und Banner einstellen](#ssh-login-scripte-und-banner-einstellen)
- [Beim Cinnamon Desktop den letzten User anzeigen](#beim-cinnamon-desktop-den-letzten-user-anzeigen)
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
deb https://deb.debian.org/debian/ trixie main contrib non-free non-free-firmware
deb https://security.debian.org/debian/ trixie-security main contrib non-free non-free-firmware
deb https://deb.debian.org/debian/ trixie-updates main contrib non-free non-free-firmware
deb https://deb.debian.org/debian/ trixie-proposed-updates main contrib non-free non-free-firmware
deb https://deb.debian.org/debian/ trixie-backports main contrib non-free non-free-firmware

#deb-src https://deb.debian.org/debian/ trixie main contrib non-free non-free-firmware
#deb-src https://security.debian.org/debian/ trixie-security main contrib non-free non-free-firmware
#deb-src https://deb.debian.org/debian/ trixie-updates main contrib non-free non-free-firmware
#deb-src https://deb.debian.org/debian/ trixie-proposed-updates main contrib non-free non-free-firmware
#deb-src https://deb.debian.org/debian/ trixie-backports main contrib non-free non-free-firmware
```
Notiz:</br>
Die Verwendung von `contrib` und  `non-free` ist umstritten.
Solange alle Pakete gefunden werden und laufen, am besten weglassen.
Wenn man jedoch einmal Pakete daraus installiert hat, kann man die Repos nicht ohne weiteres deaktivieren.
Das kann zu Problemen später führen.

Eventuell noch die `i386` Pakete hinzufügen
```
sudo dpkg --add-architecture i386
```

Man kann auch checken, welche Haupt- und zusätzliche Architekturen installiert sind.
```
dpkg --print-architecture
dpkg --print-foreign-architecture
```

Einmal alles durchstarten
```
sudo apt clean
sudo apt update
sudo apt full-upgrade
sudo apt --purge autoremove
```

### `apt` modernisieren
Seit Debian 13 Trixie wird das neue `deb822` Format standardmäßig genutzt, ähnlich des YAML-Formats.</br>
Es ersetzt bestehende .list-Dateien (z. B. `/etc/apt/sources.list` oder Dateien in `/etc/apt/sources.list.d/`) durch neue .sources-Dateien.

Mit diesem Befehl wandelt `apt` alle aktuell gültigen Repositorys um:
```
sudo apt modernize-sources
```

### `nala` installieren
Nala ist ein modernes `apt` Frontend.
```
sudo apt install nala -y
```

### `cups` ruhigstellen
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
#!/bin/bash
### eigene Aliase mit Nala als APT Frontend
alias aktualisieren='sudo nala clean && sudo nala upgrade'
alias aufraeumen='sudo nala autopurge'
alias herunterfahren='aktualisieren && sudo poweroff'
alias neustarten='aktualisieren && sudo reboot'
alias wetter='curl de.wttr.in/roth?0'

### original APT Befehle und Backups
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

Um weitere Zeilen dynamisch zu generieren, muss ein zusätzliches Script angelegt werden.
```
sudo nano /etc/update_motd.d/20-fastfetch
```
Mit folgendem Inhalt wie ein sh Script:
```
#!/bin/bash
fastfetch
```
Sie werden in numerisch aufsteigender Reihenfolge ausgeführt.

Zum Schluss das Script noch ausführbar machen:
```
sudo chmod +x /etc/update-motd.d/20-neofetch
```

## Beim Cinnamon Desktop den letzten User anzeigen
Mit diesem Befehl checken, ob wirklich der LightDM benutzt wird.
```
cat /etc/X11/default-display-manager
```

Folgende Zeile in der `/etc/lightdm/lightdm.conf` auskommentieren:
```
greeter-hide-users=false
```

## Sinnvolle Pakete
Must-Have:
```
fastfetch
└── neofetch-like tool for fetching system information
btop
└── Modern and colorful command line resource monitor that shows usage and stats
iperf3
└── Internet Protocol bandwidth measuring tool
sl
└── Correct you if you type `sl' by mistake
```
Optional:
```
atop
└── Monitor for system resources and process activity
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
sysstat
└── system performance tools for Linux
linux-perf
└── Performance analysis tools for Linux
hardinfo2
└── System Information and Benchmark for Linux Systems
```
Depricated oder Obsolete:
```
neofetch
└── Shows Linux System Information with Distribution Logo
net-tools
└── NET-3 networking toolkit
hardinfo
└── transitional package [hardinfo2]
```
