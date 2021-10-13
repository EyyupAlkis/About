---
layout: post
title: "Einführung in die Welt der Graphen"
categories:
  - "Lernblog"
  - "Graphentheorie"
lang: de-DE
---

Graphen sind eine Datenstruktur, mit der komplexe Beziehungen zwischen Daten dargestellt werden können.

![Deutschland und Nachbarländer](../assets/graphen_einführung/deutschland_nachbarn.svg)

<!--more-->

## Terminologie

Ein Graph besteht aus *Kanten* und *Knoten* (engl.: *Vertex* und *Edge*).
Im obrigen Beispiel sind als Knoten Deutschland und seine Nachbarländer zu sehen.
Als Kante bezeichnet man die Verbindung zweier Knoten, im Beispiel die gemeinsamen Grenzen.

Man kann durch einen Graphen einen *Pfad* legen.
Dieser beschreibt eine bestimmte Reihenfolge, in dem man die einzelnen Knoten besucht.
Eine Reise-Route von Belgien nach Polen über die Schweiz könnte zum Beispiel der Pfad {% latex %}BE \rightarrow DE \rightarrow CH \rightarrow AT \rightarrow CZ \rightarrow PL{% endlatex %} sein.

![Belgien nach Polen](../assets/graphen_einführung/deutschland_nachbarn_pfad.svg)

## Besondere Pfade

### Euler-Tour, Euler-Pfad

Eine *Euler-Tour* ist ein Pfad, der jeden Kante des Graphen genau einmal besucht.
Damit eine Euler-Tour zugleich ein *Euler-Pfad* ist, müssen Anfangs- und Endknoten verbunden sein.

Ein Euler-Pfad durch das *Haus des Nikolaus*:

![Haus vom Nikolaus](../assets/graphen_einführung/haus_vom_nikolaus.gif)

Ein Graph besitzt eine Euler-Tour, wenn alle Knoten eine gerade Kardinalität, also eine gerade Anzahl an anliegenden Kanten, besitzen.

### Hamilton-Zyklus

Ein *Hamilton-Zyklus* ist ein Pfad, der jeden Knoten des Graphen einmal enthält.

![Hamilton-Zyklus](../assets/graphen_einführung/Hamiltonian_path.svg)

Jede Euler-Tour ist zugleich ein *Hamilton-Zyklus*.
Im Gegensatz zur Euler-Tour, die sich leicht finden lässt, ist das Finden eines Hamilton-Zyklus NP-vollständig.

## Gerichtete Graphen

Eine Kante verbindet zwei Punkte.
Wenn sie zusätzlich eine Richtung enthält, entlang der sie verbindet, spricht man von einem *gerichteten* Graphen.

{% graphviz %}
digraph {
  scale=0.7
  Alice -> Bob
  Bob -> Carl
  Bob -> Alice
  Gustav -> Friedrich
  Friedrich -> Elena
  Elena -> Dora
  Elena -> Carl
  Dora -> Bob
  Friedrich -> Carl
  Alice -> Elena
  Gustav -> Alice
  Friedrich -> Alice
  Friedrich -> Bob
  Friedrich -> Gustav
}
{% endgraphviz %}

Dieser Graph stellt ein kleines soziales Netwerk dar.
Eine Kante {% latex %}A \rightarrow B{% endlatex %} bedeutet hier "A folgt B",
im Beispiel folgt *Gustav* also *Friedrich* und *Alice*.

Ein gerichteter Graph darf nur gerichtete Kanten enthalten, ein ungerichteter Graph nur ungerichtete.
Eine ungerichtete Kante lässt sich immer durch zwei gerichtete Kanten zwischen den beiden Knoten übersetzen.

## Gewichtete Kanten

Man kann Graphen um eine Gewichtung einzelner Kanten erweitern.
Dann kann eine Kante neben der Information, welche beiden Knoten miteinander verbunden sind, auch zusätzliche Informationen speichern.

{% graphviz %}
digraph {
  DE -> NL [label="85,886 Mrd €", weight="85.886.368.000"];
  NL -> DE [label="91,373 Mrd €", weight="91.373.944.000"];
  DE -> BE [label="44,268 Mrd €", weight="44.268.388.000"];
  BE -> DE [label="44,268 Mrd €", weight="44.268.388.000"];
  DE -> LU [label="5,773 Mrd €", weight="5.773.964.000"];
  LU -> DE [label="3,419 Mrd €", weight="3.419.324.000"];
  NL -> BE [label="51,883 Mrd €", weight="51.883.704.699"];
  BE -> NL [label="48,176 Mrd €", weight="48.176.139.425"];
  NL -> LU [label="1.265 Mrd €", weight="1.265.722.812"];
  LU -> NL [label="1.014 Mrd €", weight="1.014.461.856"];
  BE -> LU [label="6.582 Mrd €", weight="6.582.281.453"];
  LU -> BE [label="2.813 Mrd €", weight="2.813.947.802"];
}
{% endgraphviz %}

In diesem Graph sieht man zum Beispiel die Handelsvolumen zwischen Deutschland, Belgien, Luxemburg und den Niederlanden.

## TL;DR

Graphen bestehen aus *Knoten* und *Kanten*, die jeweils zwei Knoten mit einander verbinden.
Enthalten die Kanten Richtungsinformationen, so spricht man von einem *gerichteten* Graphen.
Speicher die Kanten Werte, so spricht man von einem *gewichteten* Graphen.

Mit Graphen kann man einfach komplexe Beziehungen, zum Beispiel ein soziales Netzwerk oder eine Karte, darstellen.
