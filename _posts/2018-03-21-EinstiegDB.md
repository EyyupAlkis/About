---
layout: post
title: Einstieg Datenbanken
categories:
  - Lernblog
  - Databases
lang: de
---

Als nächstes Thema des Leistungskurses werden *Datenbanken* behandelt, im speziellen *Relationale Datenbanken*.

Ein RDBMS (**R**elational **D**ata**B**ase **M**anagement **S**ystem) verwaltet Daten mithilfe sog. *Relationen*, also Beziehungen zwischen Daten.
Jeder Datensatz wird durch eine Zeile in einer *Tabelle* repräsentiert, jede *Tabelle* wird durch ein *Schema* definiert.
Ein solches Schema beschreibt die *Attribute* (Spalten) einer Entität, deren Datentypen sowie bestimmte *Restriktionen*.

<img alt="DB Symbol" src="../assets/legacy_gs_bucket/Linecons_database.svg" width="400px"/>

<!--more-->

## Beispiel

Die Tabelle *Buch* ist eine Projektion aller Bücher der Welt.
Jedes Buch wird durch die Attribute *ISBN*, *Titel*, *Preis*, *AutorID* und *VerlagID* beschrieben.
Deren Datentypen sind *Zahl*, *Text*, *Zahl*, *Zahl*, *Zahl*.

| ISBN   | Titel   | Preis   | AutorID | VerlagID |
| ------ | ------- | ------- | ------- | -------- |
| *Zahl* | *Text*  | *Zahl*  | *Zahl*  | *Zahl*   |

> *Primärschlüssel:* Menge an Attributen, durch die sich jede mögliche Entität eindeutig identfizieren lässt

Die *ISBN* dient als Primärschlüssel, da sie jedes Buch eindeutig identifiziert.

| ISBN | Titel | Preis | AutorID | VerlagID |
| ---- | ----- | ----- | ------- | -------- |
| *PK* |       |       |         |          |

> *Fremdschlüssel:* Attribut, das auf andere Entität verweist

*AutorID* und *VerlagID* verweisen auf einen Datensatz in der Tabelle *Autor* bzw. *Verlag*.
Dabei sind *AutorID* und *VerlagID* *Fremdschlüssel*, sie referenzieren einen *Primärschlüssel*.

| ISBN | Titel | Preis | AutorID | VerlagID |
| ---- | ----- | ----- | ------- | -------- |
|      |       |       | *FK*    | *FK*     |

> *Redundanz:* Daten werden mehrmals gespeichert. Man kann sie löschen, ohne dass Information verloren geht.

Ist in einer Tabelle sowohl das Buch "1 bis 5 - Mathe Teil 1" als auch *5 bis 10 - Zahlen, bitte" gespeichert, und beide Einträge speichern die Informationen über den Autor *Claus Numerus*, so sind diese Daten redundant.
Redundante Daten sind für *Anomalien* anfällig.

| ISBN | Titel                    | Autor         |
| ---- | ----------------------   | ------------- |
| 1234 | 1 bis 5 - Mathe Teil 1   | Claus Numerus |
| 1235 | 5 bis 10 - Zahlen, bitte | Claus Numerus |

> *Anomalie:* Problem oder Fehler, der bei Schreib-Operationen entsteht

Ändert der Autor nun seinen Nachnamen auf *Claus Kleber*, so müssen alle Einträge im Buch angepasst werden.
Tritt dabei ein Fehler auf, äußert sich das in einer *Inkonsistenz*.

| ISBN | Titel                    | Autor         |
| ---- | ----------------------   | ------------- |
| 1234 | 1 bis 5 - Mathe Teil 1   | Claus Numerus |
| 1235 | 5 bis 10 - Zahlen, bitte | Claus Kleber  |

> *Inkonsistenz:* Widerspruch innerhalb der Datenbank. Wird durch Anomalie hervorgerufen.

Nun verweisen die zwei Einträge auf zwei unterschiedliche Autoren, obwohl es eigentlich der selbe ist.

| ISBN | Titel                    | Autor                                         |
| ---- | ------------------------ | --------------------------------------------- |
| 1234 | 1 bis 5 - Mathe Teil 1   | Claus <span style="color: red">Numerus</span> |
| 1235 | 5 bis 10 - Zahlen, bitte | Claus <span style="color: red">Kleber</span>  |

