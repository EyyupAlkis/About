---
layout: post
title: Relationales Modell
categories:
  - Lernblog
  - Databases
lang: de
---

Um die Planung nun in eine Datenbank umzusetzen, erstellt man ein *Relationales Modell*.
Dieses teilt das ER-Modell in Tabellen und Attribute ein.

Es ist wiefolgt aufgebaut:

**TabellenName**(<i><u>Attribut1</u></i>, *Attribut2*, &uarr;*Attribut3*, *Attribut4*...)  

Alle Attribute, die dem Primärschlüssel angehören, werden <u>unterstrichen</u>.
Einem Attribut, welches als Fremdschlüssel fungiert, wird ein &uarr;Aufwärts-Pfeil vorgestellt.

<!--more-->

Eine Entität ist lässt sich grundätzlich einfach in das *Relationale Modell* überführen, das `Auto` würde so aussehen:

**Auto**(<u>FahrgestellNr.</u>, Felgengröße, Farbe, Baujahr)

### Relationen

Möchte man nun die Relationen berücksichtigen, so muss man folgende Regeln beachten:

#### 1:1
Entitäten und Attribute der Relation werden in eine Tabelle zusammengezogen.

**Fahrer**(<u>FührerscheinNr.</u>, Name, Alter, &uarr;SchlüsselId)  
**Schlüssel**(<u>SchlüsselId</u>)

#### 1:n
Auf der {% latex %}n{% endlatex %}-Seite der Relation wird ein *Fremdschlüssel* zur {% latex %}1{% endlatex %}-Seite der Relation gespeichert.
Auch die Attribute der Relation werden auf der {% latex %}n{% endlatex %}-Seite festgehalten.

**Auto**(<u>FahrgestellNr.</u>, Felgengröße, Farbe, Baujahr)  
**Schlüssel**(<u>SchlüsselId</u>, &uarr;AutoFahrgestellNr.)

#### n:m
Es wird eine zusätzliche Tabelle angelegt, in der Beziehungen festgehalten werden.
In dieser sind auch die Attribute der Relation enthalten.

**Auto**(<u>FahrgestellNr.</u>, Felgengröße, Farbe, Baujahr)  
**Fahrer**(<u>FührerscheinNr.</u>, Name, Alter)  

**fährt**(&uarr;<u>FahrerFührerscheinNr.</u>, &uarr;<u>AutoFahrgestellNr.</u>)  

### Beispiel

Im vorhergehenden Post haben wir dieses Entity-Relationship-Diagram erstellt:

![ERD](../assets/legacy_gs_bucket/erd-vollständig.png)

<style>
  .verkauft {
    padding: 2pt;
    border-radius: 15pt;
    border-spacing: 100px;
    background: #09ABF6;
    color: white;
  }
  .besitzt {
    padding: 2pt;
    border-radius: 15pt;
    background: #304FFE;
    color: white;
  }
  .oeffnet {
    padding: 2pt;
    border-radius: 15pt;
    background: #F5A623;
    color: white;
  }
  .faehrt {
    padding: 2pt;
    border-radius: 15pt;
    background: #F83E4B;
    color: white;
  }
</style>

Das Relationale Modell sieht nach den obigen Regeln so aus:

**Auto**(<u>FahrgestellNr.</u>, Felgengröße, Farbe, Baujahr, <span class="verkauft">&uarr;HändlerUSt-IdNr.</span>, <span class="verkauft">Kaufdatum</span>)  
**Fahrer**(<u>FührerscheinNr.</u>, Name, Alter, &uarr;<span class="besitzt">SchlüsselId</span>)  
**Händler**(<u>USt-IdNr.</u>, Name, Adresse)  
**Schlüssel**(<u>SchlüsselId.</u>, &uarr;<span class="oeffnet">öffnet</span>)  

**fährt**(<span class="faehrt">&uarr;<u>FahrerFührerscheinNr.</u></span>, <span class="faehrt">&uarr;<u>AutoFahrgestellNr.</u></span>)  

Die einzelnen Beziehungen sind farbig markiert:  
<span class="verkauft">verkauft</span>  
<span class="besitzt">besitzt</span>  
<span class="oeffnet">öffnet</span>  
<span class="faehrt">fährt</span>  

## TL;DR

Mithilfe des *relationalen Modells* lässt sich ein ER-Modell in ein Datenbank-Schema überführen.  
Bei {% latex %}1:1{% endlatex %}-Relationen werden die Entitäten zusammengezogen, bei {% latex %}1:n{% endlatex %}-Relationen wird der Fremdschlüssel auf der {% latex %}n{% endlatex %}-Seite gespeichert, bei {% latex %}n:m{% endlatex %}-Relationen muss eine zusätzliche Join-Table angelegt werden.  
Falls es Relations-Attribute gibt, werden diese in der gleichen Tabelle wie der Fremdschlüssel gespeichert.