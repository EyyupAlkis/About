---
layout: post
title: Einstieg SQL
categories:
  - Lernblog
  - Databases
  - SQL
lang: de
---

*SQL* oder auch ***S**tructured **Q**uery **L**anguage* ist eine Sprache, die fast alle relationalen Datenbanken unterstützen.
Sie wurde in den '70er Jahren von Donald D. Chamberlin und Raymond F. Boyce zunächst als *SEQUEL* entworfen, bevor sie später in *SQL* umbenannt wurde.
Dadurch, dass sie deklarativ aufgebaut ist, kann sie recht einfach erlernt werden.

<img src="../assets/legacy_gs_bucket/sqldatabase.svg" alt="SQL Image" width="400px"/>

<!--more-->

Im Unterricht haben wir das SQL-Tutorial auf [sqlzoo.net](https://sqlzoo.net/) durchgearbeitet.
Die darin erklärten Befehle, Funktionsweisen und Muster werden im folgenden noch einmal erläutert.
Für ein besseres Verständnis empfehle ich dennoch, das Tutorial selbst zu befolgen, um ein wenig dem Umgang mit SQL zu trainieren.

Die Tabellen, an denen die Beispiele dieses Posts durchgeführt werden, haben dieses Relationale Schema:

**Buch**(<u>BuchId</u>, Titel, Preis, &uarr;VerlagId, Jahr)  
**Autor**(<u>AutorId</u>, Name, Vorname, Geburtsdatum)  
**Verlag**(<u>VerlagId</u>, Name, Addresse)  
**schreibt**(&uarr;<u>AutorId</u>, &uarr;<u>BuchId</u>)  

## 'SELECT FROM', 'WHERE', 'LIKE'
```sql
SELECT BuchId, Titel, Preis FROM Buch
```
Mit einem `SELECT`-Statement kann man Felder einer Tabelle auswählen (eng: 'Select').
Man übergibt eine Liste der Attribute, die man auswählen möchte.
Danach folgt hinter dem Keyword `FROM` die Tabelle, auf die sich die Auswahl bezieht.
Die oben stehende Anfrage könnte zum Beispiel folgendes Ergebnis zurückliefern:

| BuchId | Titel            | Preis |
| ------ | ---------------- | ----- |
| 1      | Herr der Ringe   | 20    |
| 2      | Herr der Diebe   | 10    |
| 3      | Herr der Fliegen | 15    |
| 4      | Hans im Glück    | 7     |

Möchte man alle Attribute einer Tabelle erhalten, so kann man dafür die Kurzschreibweise `SELECT *` verwenden.

Um die Auswahl einzugrenzen, verwendet man `WHERE`.

```sql
SELECT Titel FROM Buch
WHERE Preis > 10
```

| Titel            |
| ---------------- |
| Herr der Ringe   |
| Herr der Fliegen |

Hinter dem Keyword `WHERE` wird nun ein Vergleich ergänzt, der die Auswahl einschränkt.
Es können die normalen Logischen Vergleiche verwendet werden; `AND`, `OR` und `NOT` stellen einige der bool'schen Operatoren dar.
Mit `IN` lässt sich prüfen, ob ein Wert in einer Liste vorhanden ist.

```sql
SELECT BuchId, Titel, Preis, Jahr FROM Buch
WHERE BuchId IN (1, 2) AND Jahr > 2000
```

| Titel            | Jahr |
| ---------------- | ---- |
| Herr der Diebe   | 2005 |

Mit `BETWEEN` lässt sich überprüfen, ob ein Wert innerhalb eines Intervals liegt.

```sql
SELECT Titel, Jahr FROM Buch
WHERE Jahr BETWEEN 2000 AND 2010
```

| Titel          | Jahr |
| -------------- | ---- |
| Herr der Diebe | 2005 |

Mit `LIKE` kann man einen Text-Wert mit einem Muster vergleichen.
Dabei steht '%' für beliebig viele Buchstaben, '_' für genau einen.

```sql
SELECT BuchId, Titel FROM Buch
WHERE Titel LIKE 'Herr der _i%'
```

| BuchId | Titel          |
| ------ | -------------- |
| 1      | Herr der Ringe |
| 2      | Herr der Diebe |

## 'ORDER BY'
Mit `ORDER BY` kann man eine Tabelle sortieren.

```sql
SELECT BuchId, Titel, Preis FROM Buch
ORDER BY Preis
```

| BuchId | Titel            | Preis |
| ------ | ---------------- | ----- |
| 4      | Hans im Glück    | 7     |
| 2      | Herr der Diebe   | 10    |
| 3      | Herr der Fliegen | 15    |
| 1      | Herr der Ringe   | 20    |

Ergänzt man diesen Befehl um ein `ASC` (aufsteigend) oder `DESC` (absteigend), so kann man die Richtung festlegen.
Da die Standard-Sortierrichtung `ASC` ist, fällt dieses Keyword meistens weg.

```sql
SELECT BuchId, Titel, Preis FROM Buch
ORDER BY Preis DESC
```

## 'GROUP BY', 'SUM', 'COUNT'
Oft möchte man gar nicht die einzelnen Zeilen kennen, sondern aus ihnen einen Wert berechnen.
Dafür verwendet man `GROUP BY`, danach folgt eine Operation oder ein Attributname.
Mit `SUM`, `COUNT`, `MAX`, `MIN`, `AVG` usw. kann man dann die gruppierten Spalten aggregieren.

```sql
SELECT COUNT(*), AVG(Preis) FROM Buch
GROUP BY Titel Like 'Herr der %'
```

| COUNT(*) | AVG(Preis) |
| -------- | ---------- |
| 3        | 15         |
| 1        | 1          |

## INNER 'SELECT'
Manchmal möchte man in einer `WHERE`-Clause einen Vergleich mit einem anderen Wert aus der Datenbank  anstellen.
Damit dafür nur eine Anfrage notwendig ist, kann man `SELECT` auch innerhalb eines anderen Befehl verwenden:

```sql
SELECT Titel FROM Buch
WHERE VerlagId = (
  SELECT VerlagId FROM Verlag
  WHERE Name = 'Dressler'
)
```

| Titel          |
| -------------- |
| Herr der Diebe |

Wenn die Innere Auswahl nicht nur eine Zeile zurückliefert, sondern mehrere, so kann man diese mit `IN` vergleichen:

```sql
SELECT Titel FROM Buch
WHERE VerlagId IN (
  SELECT VerlagId FROM Verlag
  WHERE name IN ('Dressler', 'Klett-Cotta')
)
```

| Titel          |
| -------------- |
| Herr der Diebe |
| Herr der Ringe |

## 'JOIN'
Mit einem *Inner* `Select` kann man grundlegende Beziehungen zwischen Tabellen abfragen.
Für komplexere Anfragen wird der `JOIN`-Befehl verwendet.

```sql
JOIN [Tabelle] ON [Logischer Vergleich]
```

Einem Join wird übergeben, welche Tabelle verbunden werden soll, und zusätzlich ein Vergleich, mit dem die Zusammengehörigkeit zweier Zeilen überprüft wird.
Möchte man zum Beispiel zu jeder Zeile der Tabelle 'Buch' die zugehörige Zeile in der Tabelle 'Verlag' erhalten, vergleicht man das Attribut `VerlagId`.

```sql
SELECT Titel, Jahr, Name FROM Buch
JOIN Verlag ON Buch.VerlagId = Verlag.VerlagId
```

| Titel            | Jahr | Verlag        |
| ---------------- | ---- | ------------- |
| Herr der Ringe   | 1954 | Klett-Cotta   |
| Herr der Fliegen | 1954 | Faber & Faber |
| Herr der Diebe   | 2000 | Dressler      |
| Hans im Glück    | 1819 | Wünschelruthe |

Auch mehrere `JOIN`s sind möglich:

```sql
SELECT Titel, Name, Vorname FROM Buch
JOIN schreibt ON Buch.BuchId = schreibt.BuchId
JOIN Autor ON Autor.AutorId = schreibt.AutorId
```

| Titel            | Name    | Vorname           |
| ---------------- | ------- | ----------------- |
| Herr der Ringe   | Tolkien | John Ronald Reuel |
| Herr der Fliegen | Golding | William           |
| Herr der Diebe   | Funke   | Cornelia          |
| Hans im Glück    | Grimm   | Wilhelm           |
| Hans im Glück    | Grimm   | Jacob             |

## Fazit

Mit SQL lassen sich große Datenmengen effizient abfragen.
Es ist angenehm, dass man nicht spezifizieren muss, wie diese Daten ermittelt werden - hier wird durch die SQL-Engines sehr viel optimiert.
In den nächsten Wochen werden `INSERT`- und `UPDATE`-Statements behandelt, außerdem werden wir uns anschauen wie man mit *DDL* eine Tabelle erzeugt.
