---
layout: post
title: Sortierverfahren
categories:
- Lernblog
- Algorithms
lang: de
---
Ein wichtiges Problem der Informatik ist das Sortieren: Wie bringe ich große Datensätze effizient in eine sinnige Reihenfolge?

Drei Ansätze haben wir nun kennengelernt:

### BubbleSort
Hier "steigt" der größte Wert einer Datenreihe immer an die Spitze - daher der Name. Dafür vergleicht man von links nach rechts alle benachbarten Werte. Sind diese nicht in der richtigen Reihenfolge, so werden sie getauscht. Man kann nun mit Sicherheit sagen, dass der größte Wert rechts steht. Nun wird dies wiederholt, allerdings werden die bereits sortierten Werte außenvor gelassen.

[![Bubble Sort](https://upload.wikimedia.org/wikipedia/commons/c/c8/Bubble-sort-example-300px.gif "Bubble Sort")](https://upload.wikimedia.org/wikipedia/commons/c/c8/Bubble-sort-example-300px.gif)

### Insertion Sort  
Hier wird jedes Element gesondert betrachtet und in eine zweite Liste einsortiert. Dafür wird es von links nach rechts mit den bereits bestehenden Elementen verglichen. Sobald es ein kleineres gefunden wird, wird es rechts daneben eingefügt.

[![Insertion Sort](https://upload.wikimedia.org/wikipedia/commons/0/0f/Insertion-sort-example-300px.gif  "Insertion Sort")](https://upload.wikimedia.org/wikipedia/commons/0/0f/Insertion-sort-example-300px.gif)

### Selection Sort
Hier wird das kleinste Element der Reihe gesucht und an eine zweite Liste angehangen.

[![Selection Sort](https://upload.wikimedia.org/wikipedia/commons/6/6d/Selsort_de_0.gif "Selection Sort")](https://upload.wikimedia.org/wikipedia/commons/6/6d/Selsort_de_0.gif)

Unter der folgenden Url sind alle gängigen Sortieralgorithmen sehr anschaulich bezüglich ihrer Rechen/Speicher-Effizient dargestellt:

[http://bigocheatsheet.com/ ](http://bigocheatsheet.com/)