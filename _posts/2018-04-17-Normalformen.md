---
layout: post
title: Normalformen
categories:
  - Lernblog
  - Databases
lang: de
---

Möchte man eine bestehende Datenbank optimieren, so lohnt es sich, sie durch die sogenannten *Normalformen* umzuformen.

Jede der drei Normalformen beschreibt jeweils eine Anforderung an die Datenbank.

<!--more-->

### Erste Normalform (1NF)

> Eine Tabelle befindet sich in der **ersten Normalform**, wenn alle Attribute einen atomaren Wertebereich haben. [^1]

Ein Wertebereich ist *atomar*, wenn man einen Wert nicht mehr in kleinere herunterbrechen kann.

Ein Beispiel für einen nicht-atomaren Wertebereich:

**Schüler**(<u>SchülerId</u>, Kurse, Schwerpunkte, Trakte)

| <u>SchülerId</u> | Kurse             | Schwerpunkte             | Trakte |
| ---------------- | ----------------- | ------------------------ | ------ |
| 1                | Deutsch, Englisch | sprachlich, sprachlich   | S, S   |
| 2                | Englisch, Kunst   | sprachlich, künstlerisch | S, K   |

Mit atomarem Wertebereich:

**Schüler**(<u>SchülerId</u>, <u>Kurs</u>, Schwerpunkt, Trakt)

| <u>SchülerId</u> | <u>Kurs</u> | Schwerpunkt | Trakt |
| ---------------- | ----------- | ----------- | ----- |
| 1 | Deutsch | sprachlich | S |
| 1 | Englisch | sprachlich | S |
| 2 | Englisch | sprachlich | S |
| 2 | Kunst | künstlerisch | K |

In der umgeformten Tabelle kann man besser suchen, da jeder Wert einzeln gelistet ist.

### Zweite Normalform (2NF)

> Ein Datenbankschema ist in der **zweiten Normalform**, wenn es in der 1NF ist und zusätzlich jedes Nichtschlüsselattribut vom Primärschlüssel voll funktional abhängig ist und nicht bereits von einem Teil der Schlüsselattribute. [^1]

Ein Attribut B ist vom Attribut A voll funktional abhängig, wenn B allein durch A bestimmt wird.
Man notiert: {% latex %}A \rightarrow B{% endlatex %}

Im obigen Beispiel ist das Attribut `Schwerpunkt` voll funktional abhängig vom Attribut `Kurs`: Der Schwerpunkt eines Kurses hängt nur vom Kurs ab.  
Möchte man diese funktionale Abhängigkeit vermeiden, so lagert man sie in eine zweite Tabelle aus.

**Schüler**(<u>SchülerId</u>, &uarr;<u>Kurs</u>)

| <u>SchülerId</u> | &uarr;<u>Kurs</u> |
| - | - |
| 1 | Deutsch |
| 1 | Englisch |
| 2 | Englisch |
| 2 | Kunst |

**Kurs**(<u>Kurs</u>, Schwerpunkt, Trakt)

| <u>Kurs</u> | Schwerpunkt | Trakt |
| - | - |
| Deutsch | sprachlich | S |
| Englisch | sprachlich | S |
| Kunst | künstlerisch | K |

### Dritte Normalform (3NF)

> Ein Datenbankschema ist in der **dritten Normalform**, wenn es in der 2NF ist und es zusätzlich kein Nichtschlüsselattribut gibt, das transitiv abhängig von einem Schlüsselattribut ist.
Es darf also keine funktionalen Abhängigkeiten zwischen Nichtschlüsselabhängigkeiten geben. [^1]

Ein Attribut C ist vom Attribut A transitiv abhängig, wenn es voll funktional abhängig von einem Attribut B ist und B voll funktional abhängig ist von A und A nicht funktional abhängig von B ist.
Man notiert: {% latex %}A \rightarrow B \rightarrow C{% endlatex %}

Im obigen Beispiel ist das Attribut `Trakt` transitiv abhängig vom Schlüsselattribut `Kurs`: Der Trakt ist allein vom Schwerpunkt abhängig, und der Schwerpunkt allein vom Kurs.

Um die Tabelle ohne transitive Abhängigkeiten zu speichern, müssen die Informationen über die Schwerpunkte in eine neue Tabelle extrahiert werden:

**Kurs**(<u>Kurs</u>, &uarr;Schwerpunkt)

| <u>Kurs</u> | &uarr;Schwerpunkt |
| - | - |
| Deutsch | sprachlich |
| Englisch | sprachlich |
| Kunst | künstlerisch |

**Schwerpunkt**(<u>Schwerpunkt</u>, Trakt)

| <u>Schwerpunkt</u> | Trakt |
| - | - |
| sprachlich | S |
| künstlerisch | K |

## TL;DR

Die drei Normalformen beschreiben Anforderungen, die eine gut strukturierte Datenbank erfüllen sollte.

Die *1NF* verbessert die Durchsuchbarkeit der Daten, indem sie alle Werte auf den kleinst-sinnvollen Bereich verkleinert.  
*2NF* sowie *3NF* eliminieren Redundanzen, indem sie die Abhängigkeit der Werte vom Schlüsselattribut untersuchen.

## Beispiel-Umformung



[^1]: Quelle: [Schöningh, Informatik - Lehrwerk für die gymnasiale Oberstufe - Neubearbeitung, 978-3-14-037127-8](https://verlage.westermanngruppe.de/schoeningh/artikel/978-3-14-037127-8/Informatik-Lehrwerk-fuer-die-gymnasiale-Oberstufe-Neubearbeitung-Schuelerband-2)
