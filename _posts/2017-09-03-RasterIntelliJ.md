---
layout: post
title: IntelliJ statt Java-Editor
description: Raster aus dem Unterricht in IntelliJ Importieren - Das Tutorial
comments: true
lang: de
categories:
  - Lernblog
---

Möchte man im Informatik-Unterricht nicht mehr den Java-Editor, sondern IntelliJ benutzen, muss man beim Importieren der Rasteraufgaben ein paar Dinge beachten.
Dafür habe ich unten für zwei Möglichkeiten die Schritte notiert.
Möglichkeit 1 hat den Vorteil, dass man bei der Erstellung des Projektes noch ein paar Dinge einstellen kann. Möglichkeit 2 geht im Gegenzug ein wenig schneller.
<!--more-->
![IntelliJ Welcome Screen](../assets/legacy_gs_bucket/IntelliJ%20Welcome%20Screen.png "IntelliJ Welcome Screen")

### Möglichkeit 1 - Dateien in IntelliJ Projekt einfügen

1. Raster Entzippen
2. Neues IntelliJ Projekt anlegen (Create New Project)
	* Template kann man ignorieren
3. Rasterdateien per Drag’n’Drop in IntelliJ ziehen (unter `src`)
4. Falls IntelliJ meckert, dass die Datei mit dem falschen Encoding geöffnet wurde:
	* “Reload in another encoding” drücken
	* `ISO-8859-1` auswählen
	* “Reload” drücken

5. Fertig

![Reload in another encoding](../assets/legacy_gs_bucket/IntelliJ%20Load%20In%20other%20encoding.png "Reload in another Encoding")

### Möglichkeit 2 - Mit IntelliJ Raster-Ordner öffnen

1. Raster Entzippen
2. IntelliJ “Open”, den Ordner auswählen
3. In IntelliJ Unterordner `src` erstellen
4. Alle .java Dateien in `src` verschieben
5. `src` als “Sources Root” definieren
6. "Project SDK" einstellen (Nur falls aufgefordert durch gelben Balken)
6. Bei Möglichkeit 1, Schritt 4 fortfahren

![Mark As Sources Root](../assets/legacy_gs_bucket/IntelliJ%20Tutorial%20mark%20as%20sources%20root.png "Mark As Sources Root")

### Trivia
* .jfm-Dateien sind Konfigurationsdateien vom Java-Editor. Diese kann man getrost ignorieren, außer ebendiesem kann nämlich kein Programm was damit anfangen.