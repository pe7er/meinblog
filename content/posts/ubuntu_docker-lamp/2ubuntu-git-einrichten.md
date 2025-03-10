---
date: 2021-02-03
title: 'Git unter Ubuntu 20.04 einrichten'
template: post
thumbnail: '../../thumbnails/ubuntu.png'
slug: ubuntu-git-einrichten-docker-lamp
langKey: de
categories:
  - Betriebssystem
tags:
  - Linux
  - Ubuntu
  - Git
---

Ich verwalte meine Softwareprojekte mit [Github](https://github.com/astridx). So greife ich jederzeit auf verschiedene Versionsstände zurück. Um die Dateien unkompliziert in der Versionsverwaltung auf den neuen Rechner zu laden, installiere und konfiguriere ich als erstes Git.

## Voraussetzungen

Nach der Installation des Desktop Images von [Ubuntu 20.04 LTS (Focal Fossa)](https://releases.ubuntu.com/20.04/) verfüge ich über ein _non-root-Superuser-Konto_ und kann direkt mit der Installation von Git beginnen.

## Installieren von Git mit Standardpaketen

Ich nutze die Standardinstallation.

> In den Standardpaketen ist nicht immer die aktuellste Version. Falls du die neuesten Git Funktionen nutzt, installiere https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

Git wird oft zusammen mit anderen Softwarepaketen installiert. Prüfe, ob es auf deinem Rechner schon vorhanden ist:

```
git --version

```

Überspringe diesen Schritt, wenn dir eine Version angezeigt wird, dann ist Git installiert.

```
version 2.25.0

```

Wird dir keine Git-Versionsnummer angezeigt? Installiere in diesem Fall Git mit APT, dem standardmäßigen Paketmanager von Ubuntu.

Verwende wie gewohnt als erstes die APT-Paketmanagement-Tools zur Aktualisierung des lokalen Paketindexes.

```
sudo apt update

```

Installiere dann Git:

```
sudo apt install git

```

Überprüfe die Installation.

```
git --version

```

Die Ausgabe sollte beispielsweise wie folgt sein:

```
version 2.25.1

```

Richte Git nach erfolgreicher Installation ein.

## Einrichten von Git

Richte Git so ein, dass es dich bei der Erstellung deiner Softwareprojekte unterstützt.

Nutze den Befehls `git config` zur Konfiguration des Namens und der E-Mail-Adresse:

```
git config --global user.name „DeinName“
git config --global user.email „deineemailadresse@example.com“

```

Überprüfe die Konfiguration:

```
git config --list

```

Die Ausgabe sollte wie folgt sein.

```
user.name=Deinname
user.email= deineemailadresse@example.com
...
```

Die Informationen werden in der Git-Konfigurationsdatei gespeichert. Mit einem Editor kann man diese einsehen und gegebenenfalls bearbeiten, beispielsweise mit [Nano](<https://de.wikipedia.org/w/index.php?title=Nano_(Texteditor)&oldid=191546214>):

```
nano ~/.gitconfig

```

Die Ausgabe sollte wie folgt sein.

```
[user]
  name = Deinname
  email = deineemailadresse@example.com

```

> Drücke `STRG` und `X`, dann `Y` und anschließend die `Eingabetaste`, um den Texteditor Nano zu verlassen.

Es gibt viele weitere [Optionen](https://docs.github.com/de). Die hier beschriebenen sind meiner Meinung nach die wichtigsten.

## Gesamtes Set

1. [Vorwort](/ubuntu-vorwort-docker-lamp)

-> 2. [Git](/ubuntu-git-einrichten-docker-lamp)

3. [Docker](/ubuntu-docker-einrichten-docker-lamp)
4. [Docker Compose](/ubuntu-docker-compose-einrichten-docker-lamp)
5. [docker-lamp einrichten](/ubuntu-docker-lamp-einrichten)
6. [docker-lamp verwenden](/ubuntu-docker-lamp-verwenden)
7. [docker-lamp mit eigenen Projekten](/ubuntu-docker-lamp-verwenden-eigene-projekte)
8. [Visual Studio Code](/ubuntu-vscode-docker-lamp)
9. [docker-lamp mit eigenen Domain](/ubuntu-docker-lamp-verwenden-eigene-domain)

<img src="https://vg02.met.vgwort.de/na/6da1ef5e40714b0481cbacda81aa9df6" width="1" height="1" alt="">
