---
layout: post
title: SQL Injections
categories:
  - Lernblog
  - Databases
  - SQL
lang: de
---

Durch eine Sicherheitslücke hat ein Hacker die Datenbank eines großen Online-Shops gelöscht.
Dabei hat er doch nur `2'; DROP TABLE products; -- ` in die Suchzeile eingegeben!

Solche Angriffe nennen sich "SQL Injection" und sind sehr gefährlich.
Wie sie funktionieren und wie eine Anwendung vor ihnen geschützt werden kann, wird im folgenden beschrieben.

![Syringe injects into SQL DB](../assets/legacy_gs_bucket/SQL%20Injection.svg)

<!--more-->

## Schwachstelle

SQL ist eine mächtige Sprache, um strukturierte Daten zu verwalten.
Jedoch genügt den wenigsten Anwendungen ein fixer Anweisungssatz, die meisten basieren auf Parametern, die vom Nutzer gestellt werden.
Ein Beispiel:

Der Onlineshop `yugiohkarten.com` hat eine Suchfunktion, mit der man den Kartenkatalog durchsuchen kann.

<img
  src="https://orig00.deviantart.net/5a5a/f/2009/252/d/4/chuck_norris_yu_gi_oh_card_by_ronnie_r15.jpg"
  alt="Chuck Norris Yugioh Card"
  style="float: right; margin-left: 2em;"
  width="30%"
/>

Die Suchfunktion ist unter der Addresse `yugiohkarten.com/search?title=ChuckNorris` erreichbar  und liefert eine HTML-Seite mit den Suchergebnissen zurück.  
Auf dem Webserver wird dafür eine SQL-Anfrage an die Datenbank gestellt, die alle Karten mit dem Titel "ChuckNorris" zurückgeben soll.  
Diese wird aus einem Template und den in der Anfrage übergebenen Parametern zusammengesetzt, die Funktion könnte ungefähr so aussehen:

```java
String searchByTitleStatement(String title) {
  return "SELECT * FROM cards WHERE title = '" + title + "';";
}
```

Solange `title` ein normaler Suchwert ist, entstehen dadurch keine Probleme.

Hat nun jemand die Idee, nach `'; DROP TABLE cards; -- ` zu suchen, lautet der resultierende Befehl:

```sql
SELECT * FROM cards WHERE title = ''; DROP TABLE cards; -- ;
```

Da SQL alle Befehle ausführt, ist danach die Tabelle "cards" nicht mehr vorhanden.

Hier wurde also in eine Suchanfrage eine Fremde SQL-Anfrage *injiziert*.
Das funktioniert so:

- `'; ` beendet die letzte Anfrage.
- `DROP TABLE cards;` ist der böswillige Befehl.
- ` -- ` macht die folgenden SQL-Statements zum Kommentar.

## Wie kann man sich davor schützen?

Um SQL-Injections zu verhindern, gibt es in vielen Sprachen fertige Lösungen.
In Java lassen sich paramtrisierte SQL-Statements vorbereiten:

```java
PreparedStatement searchTitle = connection.prepareStatement(
  "SELECT * FROM cards WHERE title = ? ;"
);
```

Dabei steht ein Fragezeichen für einen Parameter, der später eingesetzt werden muss.

```java
searchTitle.setString(1, title);
searchTitle.execute();
```

Beim Einsetzen wird der Parameter, der Datentyp und der einzusetztende Wert spezifiert, dann kann die Anfrage ausgeführt werden.

Die Funktionsweise dieser PreparedStatements ist immer gleich:
Beim einsetzen der Parameter werden die Werte auf mögliche Angriffsversuche untersucht und nötigenfalls unterbunden.
So wäre ein Angriff, wie er oben geschildert wurde, nicht mehr möglich.

## Achtung!

Das Ausnutzen einer solchen Sicherheitslücke ist nicht nur moralisch verwerflich, sondern illegal.
Falls du auf eine solche Schwachstelle stößt, gib auf jeden Fall dem zuständigen Administrator / Entwickler Bescheid!
Wenn du Interesse an dem Aufspüren solcher Sicherheitslücken hast, kann Pentesting für dich Interessant sein: Das ist ein Teilbereich der IT, in dem es darum geht, solche Sicherheitslücken in Systemen zu finden und zu beheben.
