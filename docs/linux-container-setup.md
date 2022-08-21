Title:    Linux Container (LXC) Setup  
Date:     2022-08-21  
Author:   Thomas Poth  
Keywords: LPIC, LXC, Linux, Container, Setup, Templates, TL;DR;

---

# Linux Container (LXC) Setup

In diesem Artikel zeige ich wie man aus einer Ubuntu Linux Installation mittels LXC - also Linux Containern - eine Laborumgebung aufbauen kann. Ich habe diese Umgebung beispielsweise während meines LPIC Studiums genutzt. Das hat mir immer mal wieder bei der Bereitstellung eines frischen Linux geholfen. Während des Studiums benötigte ich immer wieder eine frisch installierte Linux Version für die Laboreinheiten.

## Voraussetzungen

- Eine VM mit einer Internetverbindung
- Ubuntu Linux 20.04 LTS
- Alle Updates installiert `sudo apt update && sudo apt upgrade -y`

## TL;DR;

Hier die wichtigsten Befehle zum Umgang mit LXC.

```bash
# LXC installieren unter Ubuntu
sudo apt install lxc lxc-templates -y

# Einen neuen Container aus einem Template erstellen
sudo lxc-create -t ubuntu -n newpc

# LXC's auflisten
sudo lxc-ls
# LXC's mit Details auflisten
sudo lxy-ls --fancy

# Container detached starten
sudo lxc-start -n newpc -d

# Anmelden am Container
sudo lxy-ls --fancy
# IP Adresse unseres Containers ermitteln und im nächsten Befehl verwenden
# anstelle des Platzhalters <ip-adresse>
ssh ubuntu@<ip-adresse>

# Die Konfigurationen der einzelnen Container liegen hier:
cd /var/lib/lxc

# In jedem Container Verzeichnis liegen einige Konfigurationsdateien
# Daneben liegt der Ordner mit dem Root-Dateisystem

cd /var/lib/lxc/newpc/rootfs

# Container stoppen
sudo lxc-stop -n newpc

# Nicht mehr benötigten Container zerstören
# Container müssen vorher gestoppt sein!
sudo lxc-destroy -n newpc
```

Beim verwenden des Ubuntu-Templates werden die folgenden Default Anmeldeinformationen erzeugt:

| Default Benutzername | Initiales Kennwort |
|-|-|
| ubuntu | ubuntu |

## Benötigte Pakete installieren

Damit wir LXC verwenden können, müssen natürlich die entsprechenden Pakete installiert werden. Ich habe in meinem Fall zwei Pakete genutzt:

- `lxc`
- `lxc-templates`

Die installation der Pakete lässt sich mit dem folgenden Befehl bewerkstelligen:

```bash
sudo apt install lxc lxc-templates -y
```

> Wir verwenden hier `sudo`, da wir für die Installation der Pakete `root`-Rechte benötigen.

> Das `-y` am Ende der Befehlszeile, habe ich eingefügt, damit beim Installieren keine Rückfragen kommen ob die Pakete auch wirklich installiert werden sollen.

## Einen neuen Container aus einem Template erstellen

> Wenn ich im Folgenden von Containern rede, sind immer Linux Container (LXC) gemeint.

Um zu prüfen, dass die Installation erfolgreich war, erzeugen wir einfach einen neuen (Linux) Container. Dieser basiert auf dem `ubuntu`-Template. Der neue Container bekommt den Namen `newpc`. Hierzu nutzen wir die folgende Befehlszeile:

```bash
sudo lxc-create -t ubuntu -n newpc
```

> Dies dauert einen Augenblick, bis die Ubuntu Pakete alle installiert sind.

Wichtig sind insbesondere die letzten Zeilen der Ausgabe in der Shell:

```bash
##
# The default user is 'ubuntu' with password 'ubuntu'!
# Use the 'sudo' command to run tasks as root in the container.
##
```

Also gleich notieren:

| Default Benutzername | Initiales Kennwort |
|-|-|
| ubuntu | ubuntu |

> Was soll ich sagen?!  Das Kennwort sollte natürlich zügig geändert werden!

## Prüfen ob der neue Container existiert

Zum prüfen ob der neue Container angelegt wurde verwenden wir den folgenden Befehl:

```bash
sudo lxc-ls
```

In der Ausgabe der Befehls sollte unser neuer Container `newpc` enthalten sein:

```bash
newpc
```

## Starten des neuen Containers

Zum starten des neuen Containers verwenden wir den folgenden Befehl:

```bash
sudo lxc-start -n newpc -d
```

> Mit der Option `-d` starten wir den neuen Container `Detached` von unserer Shell. Würden wir den Schalter nicht angeben, würde der Container direct nach der Nutzung wieder beendet werden. Wir wollen aber das der Container im Hintergrund weiterläuft.

## Details zum Container anzeigen

Damit wir uns die Details zu unseren Containern ansehen können, verwenden wir den Optionsschalter `--fancy`:

```bash
sudo lxc-ls --fancy
```

Dies führt in meinem Fall zu dieser Ausgabe:

```
NAME  STATE   AUTOSTART GROUPS IPV4       IPV6 UNPRIVILEGED
newpc RUNNING 0         -      10.0.3.239 -    false
```

> In Eurem Fall weicht ggf. die IP-Adresse ab!

## Anmelden an unserem neuen Container

Wir greifen auf unseren Container per `ssh` zu. Dafür benötigen wir die IP Adresse aus dem vorherigen Kapitel. In meinem Fall war dies die Adresse `10.0.3.239`. Weiterhin benötigen wir den Benutzernamen `ubuntu` inklusive dem gleichlautenden Kennwort. Hier nun der Befehl zur Anmeldung per `ssh`:

```bash
ssh ubuntu@10.0.3.239
```

Da wir ja ein neues Linux vor uns haben und uns erstmalig an diesem System anmelden fragt werden wir gefragt ob wir dem Server vertrauen wollen. Hierzu dient die Ausgabe, die bei Euch in ähnlicher Form zu sehen sein dürfte:

```
The authenticity of host '10.0.3.239 (10.0.3.239)' can't be established.
ECDSA key fingerprint is SHA256:kU1Cjvi0DC4VYmK9hJgXbV+0GEeoGyJjFDOwYlFJbmQ.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```

Diese Rückfrage bestätigt Ihr nun durch die Eingabe von `yes`

> Vergesst anschließend nicht die Enter-Taste zu drücken ;)

Anschließend werdet Ihr aufgefordert das Kennwort des users `ubuntu` einzugeben. Sobald dies geschehen ist, solltet Ihr die folgende - oder eine so ähnliche - Mitteilung auf Eurem Bildschirm sehen:

```
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-124-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ubuntu@newpc:~$
```

Am Prompt `ubuntu@newpc:~$` erkennt Ihr, dass die Anmeldung erfolgreich war.

## Abmelden vom Container

Um sich wieder vom Container abzumelden, müsst Ihr einfach `exit` eingeben.

> Eine Shortcut dafür wäre übrigens `^+d`.

> Dabei steht das `^` für die Taste `Strg` oder bei einer englischen Tastatur `Ctrl`. Das `+` Zeichen bedeutet, dass die Taste anschließende Taste `d` zusammen mit der Taste `Strg` gedrückt werden muss.

Anschließend befinden wir uns wieder auf dem sogenannten Container-Host. Was durch den folgenden Prompt zu erkennen wäre:

```
tpoth@lpic:~$
```

> Natürlich würde in Eurem Fall vor und nach dem @ Eure Benutzername und der Hostname Eures Systems stehen.

## Anatomie eines Linux Containers

Linux Container bestehen wie alles in Linux aus ein paar Ordnern und Dateien. Um dies im Folgenden zu untersuchen wechseln wir einmal unsere Identität zu `root`. Das macht es leichter, da wir sonst vor jeden Befehl wieder `sudo` setzen müssen:

```bash
sudo su
```

Nun wechseln wir in das Verzeichnis der Container auf dem LXC-Host und schauen uns den Inhalt dieses Verzeichnisses an:

```bash
cd /var/lib/lxc
ls
```

Die Ausgabe sollte ungefähr wie folgt aussehen:

```
newpc
```

Dabei handelt es sich bei `newpc` um den Ordner unseres gerate erzeugten Containers. Um diesen etwas weiter zu untersuchen wechseln wir in diesen Ordner und lassen uns den Inhalt auflisten:

```bash
cd newpc
```

Die Dateien `config` und ggf. auch `fstab` sind Konfigurationsdateien des Containers. Daneben existiert mit dem Ordner `rootfs`, das sogenannte Root-Filesystem des Containers. Wenn wir nun in den Ordner `rootfs` wechseln sehen wir, dass sich darin ein komplettes Linux Dateisystem befindet:

```bash
cd rootfs
ls
```

Die Ausgabe sollte auch bei Euch so ähnlich aussehen:

```
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

Ich schätze, der ein oder andere wird nun sicher schon erkannt haben, dass sich auf diesem Weg die Dateien eines Containers recht einfach bearbeiten lassen.

## Nicht mehr benötigten Container zerstören

Damit wir auch wieder Platz auf unserem System schaffen können, sollten wir einen Conatiner nicht mehr benötigen, können wir Ihn mit dem folgenden Bafehl "zerstören":

```bash
sudo lxc-destroy -n newpc
```

Bevor dies jedoch funktioniert, müssen wir den laufenden Container ggf. erst noch stoppen:

```bash
sudo lxc-stop -n newpc
```

## Weitere Informationen

Das soll es zur Einrichtung von LXC gewesen sein.

Weiterführende Informationen findet man wie üblich unter Linux in den `man`-Pages und im Internet auf den Projektseiten des LXC Projektes unter https://linuxcontainers.org/lxc/introduction/