---
layout: post
title: "Eine Woche am Hasso-Plattner-Institut"
categories:
  - "Blog"
  - "HPI"
lang: de-DE
---

Die letzten fünf Tage habe ich am Hasso-Plattner-Institut in Potsdam verbracht und in einem 5er-Team an einem Hardware-Projekt gebastelt.
In diesem Blog-Eintrag möchte ich meine Erfahrungen und Eindrücke aus dem Projekt und von der Uni schildern.

Vorweg: Es war ein grandioses Camp.

<!--more-->

## Das Camp ...

... fand von Sonntag bis Donnerstag am Hasso-Plattner-Institut (HPI) in Potsdam statt und behandelte das Thema "Hardware-Programmierung mit dem ESP32".
Sechs 5er-Gruppen der Jahrgangsstufen zehn bis zwölf haben an jeweils eigenen kleinen Projekten zum Thema "Wie kann Hardware unser Leben vereinfachen?" gearbeitet.

Zu Beginn erhielten wir von den Student*innen des HPI-Schülerklubs eine Einführung in C++-Programmierung und Hardware-Programmierung mit dem ESP32, außerdem wurden uns die Basics des Projektmanagements mit Kanban und Standup-Meetings nähergebracht.

In einem (viel zu kurzen! 😃) Design-Thinking-Workshop wurde dann gemeinsam gebrainstormed, um sich schließlich auf eine Projektidee zu einigen und diese auszuarbeiten.

## Unser Projekt ...

... hört auf den Namen "TagFinder". Was ist das?

Jeder kennt "Find my Phone", mit dem man sein Smartphone aus der Ferne klingeln lassen kann, um es wieder zu finden. Ein Feature, das man häufiger verwendet, als man es sich wünschen würde ...  
*Wieso gibt es so etwas eigentlich nur für Smartphones?*

TagFinder kommt zur Rettung.  
Wir haben einen kleinen Tag mit Netzwerkanbindung gebaut, mit dem man seine Wertsachen versehen kann.
Diesen kann man dann, genau wie bei "Find My Phone", über das Internet klingeln lassen.

Das ganze funktioniert so:  
Alle Microcontroller verbinden sich mit einem WLAN-Netzwerk.
Sobald einer Microcontroller startet, meldet er sich über einen HTTP-Request bei einem zentralen Server an.
Dieser schickt dann alle paar Sekunden einen Ping an alle ihm bekannten Geräte, um herauszufinden, ob diese noch im Netzwerk sind.
Zur Steuerung gibt es ein Web-Frontend, welches die Informationen über die einzelnen Tags übersichtlich anzeigt.
Möchte man einen der Tags finden, so klickt man ihn auf der Website an.
Der Server schickt nun an diesen Tag einen weiteren HTTP-Request, um die Geräusche auszulösen.
Hat man den Tag gefunden, kann man auf einen Knopf drücken, um den Alarm zu beenden.

So lassen sich zum Beispiel Rucksäcke, Notebooks und Schlüssel einfach im Haus wiederfinden.

Ursprünglich hatten wir geplant, dass die Geräte unter einander ein Mesh-Netzwerk aufbauen, damit sie vom WLAN-Netz unabhängig funktionieren.
In der kurzen Zeit konnten wir dies nicht umsetzen, es wäre aber sicherlich eine hilfreiche Erweiterung.
Außerdem wäre es denkbar, einen weiteren Tag-Typ mit GPS und UMTS zu bauen, damit Geräte auch außerhalb des eigenen WLAN-Netzwerks getracked werden können.

An Maximilian, Stefan, Tido und Leo aus meinem Team: Wir haben ein sehr cooles Projekt auf die Beine gestellt und darauf können wir stolz sein. Es hat super viel Spaß gemacht, mit euch im Team daran zu arbeiten. Vielen Dank für die tolle Woche!

Den Code haben wir [auf GitHub](https://github.com/Skn0tt/TagFinder) veröffentlicht.

## Das HPI ...

... hat micht echt begeistert.
Besonders dass keine reine Informatik, sondern eher ein praxisorientiertes Curriculum mit Ausrichtung auf Software-Entwicklung unterrichtet wird, gefällt mir sehr.
Das Design-Thinking-Angebot und die Vorlesungen zu Recht und Wirtschaft für IT-ler sind ein weiterer Grund für mich, dort studieren zu wollen.

Die HPIler wirkten wie eine große Familie, nicht zuletzt ist dies wohl auch der geringen Studentenzahl (~500) geschuldet.
Es gibt tolle, engagierte Klubs wie den Schülerklub, der auch dieses Camp organisiert hat.
Obwohl ich für nur fünf Tage am HPI zu Gast war, habe ich mich direkt willkommen gefühlt - vielen Dank dafür! 😄

Vor zwei Stunden habe ich die Rückfahrt vom Berliner Hauptbahnhof hinter mich gebracht und bin wieder in Bonn angekommen - ich kann es kaum erwarten, wieder zurück zu nach Potsdam zu fahren.
Ich hoffe sehr, im nächsten Wintersemester am HPI studieren zu können.
Das wird toll! 😊

