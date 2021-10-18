---
layout: post
title: Modul-Aufbau
categories:
- StepSequencer
lang: de
---
Da das Projekt ein wenig umfangreicher wird, möchte ich es so modular wie möglich halten. Da in einem Cross-Plattform Szenario auch nicht der gesamte Code übertragbar ist (speziell das GUI), muss ich mit einem Modularen Aufbau auch nur einzelne Module anpassen, um die App zu portieren.

Um ein bischen Struktur ins Projekt zu bringen, ist jedes Modul zwingend so aufgebaut:

### Module Data  
Hier werden alle Variablen deklariert, die für das Modul und/oder andere Module wichtig / zugänglich sein müssen.

### Initialisierung  
Hier befinden sich alle Constructors des Moduls. Benötigt ein Modul Zugriff auf andere, so ist ein Argument des Constructors die Szene, von der aus das Modul initialisiert wird. So kann es auf alle anderen Module, die auch hier initialisiert wurden, zugreifen.

### Module Interface  
Dies sind die Methoden, auf die andere Module zugreifen können. Die meisten Module haben eine "tick()"-Methode - was diese tut, erkläre ich in einem anderen Eintrag.  
In den Interface-Methoden wird allerdings kein kritischer Code ausgeführt. Zumeist verweisen diese nur auf Methoden aus dem nächsten Programmteil:

### Module Logic  
Hier befindet sich die eigentliche Logik des Moduls. Alle Methoden hier drin sind als private deklariert.

Einige Module können auch zusätzliche Abschnitte besitzen, je nach ihrem Zweck und Umfang.  
Durch diese Struktur verspreche ich mir einigermaßen leserlichen Code, auch nach ein paar tausend Zeilen mehr Code.
