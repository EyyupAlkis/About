---
layout: post
title: "Grundsätze des Datenschutzes"
categories:
  - "Lernblog"
lang: de-DE
---

Datenschutz ist ein Thema, welches durch die DSGVO in der letzten Zeit viel Aufsehen erregt hat - und das ist auch gut so.
Es werden fast täglich neue Datenlecks bekannt, zuletzt sogar ein Vorfall bei Facebook, bei dem Passwörter im Klartext gespeichert wurden.

Um diese Fehler zu verhindern und der DSVGO zu entsprechen, gibt es einige Grundsätze, die die Basis für einen verantwortungsvollen Umgang mit Nutzerdaten bilden.

<img src="../assets/grundsätze_des_datenschutzes/personal_data.svg" style="max-width: 60%" alt="Persönliche Daten">

<!--more-->

## Die 7 Grundprinzipien des Datenschutzes

**Zweckbindung**  
Daten, die für einen bestimmten Zweck erhoben werden, dürfen auch nur für diesen Zweck verwendet werden.
Speichert ein Online-Shop zum Beispiel die E-Mail-Addresse eines Nutzers, um ihm die Rechnung zukommen zu lassen, so darf er diese nicht ungefragt für den Versand eines Newsletters verwenden.

**Verbot bei Erlaubnisvorbehalt**  
Die Erfassung personenbezogener Daten ist "Opt-In".
Das heißt: Sie ist erst einmal verboten - es sei denn, man hat die ausdrückliche Erlaubnis der betroffenen Person.
Diese Erlaubnis kann zum Beispiel im Rahmen der AGB oder der Nutzungsbedingungen erteilt werden.


**Direkterhebung**  
Personenbezogene Daten sollten direkt bei der betroffenen Person eingeholt werden.
Fremde Quellen, wie sie zum Beispiel für die von Facebook erstellten Shadow Profiles[^shadow-profiles] verwendet werden, sind kritisch zu sehen.

[^shadow-profiles]: [Shadow Profiles](https://www.dailydot.com/news/facebook-shadow-profiles-privacy-faq/)

**Datensparsamkeit**  
Daten sollten so kurz wie möglich, so lang wie nötig gespeichert werden.
Ist zum Beispiel ein Gewinnspiel beendet, so müssen die dafür eingeholten E-Mail-Addressen der Teilnehmer gelöscht werden.
Gibt es gesetzliche Aufbewahrungsfristen - zum Beispiel für Rechnungen - so haben diese natürlich Vorrang.

**Datenvermeidung**  
Es sollte so wenig wie möglich, so viel wie nötig gespeichert werden.
Es dürfen also nur Daten gespeichert werden, die für den Zweck des Dienstes unerlässlich sind.
Damit ist eine sogenannte Vorratsdatenspeicherung unzulässig.

**Transparenz**  
Ein Nutzer sollte jederzeit einsehen können, welche Daten über ihn gespeichert werden.
Dies kann über ein Datenverarbeitungsverzeichnis geschehen, in dem genau aufgeschlüsselt ist, welche Daten wie verarbeitet werden.
Sollte eine Weitergabe der Daten an Dritte geschehen, so müssen die betroffenen Nutzer darüber informiert werden.

**Erforderlichkeit**  
Personenbezogene Daten dürfen ausschließlich zur Erreichung des Zwecks gespeichert werden (siehe oben: Zweckbindung).

## Der richtige Umgang mit Passwörtern

Aus gegebenem Anlass[^zdf] möchte ich kurz darüber berichten, wie mit Passwörtern zu verfahren ist.
Die Speicherung dieser ist für fast alle Dienste unerlässlich, da sie dem Zweck der Authentifizierung des Nutzers dienen.

[^zdf]: [Passwort-Skandal: Facebook verstoesst gegen einfachste Datenschutzregeln (Quelle: ZDF)](https://www.zdf.de/nachrichten/heute/passwort-skandal-bei-facebook-verstoesst-gegen-einfachste-datenschutzregeln-100.html)

Dabei gibt es drei mögliche Vorgehensweisen:

1. Im Klartext ablegen (Bitte *niemals* machen.)
2. Verschlüsseln
3. Hashen

Passwörter im Klartext abzulegen, ist gefährlich - sie sind für jeden Mitarbeiter/Angreifer, der Zugriff auf die Datenbank erlangt, einfach auszulesen.
Auch das Verschlüsseln der Passwörter ist problematisch - findet jemand den Schlüssel heraus, zum Beispiel ein Administrator, können auch hier die Passwörter einfach ausgelesen werden.

Die dritte Möglichkeit, das *Hashen* von Passwörtern, ist der ganz klare Sieger.
Man kann sagen: Passwörter sollten **unter allen Umständen** gehasht werden.

Eine Hashfunktion ist eine Einwegfunktion - in eine Richtung kann sie ganz einfach bestimmt werden, in die andere Richtung ist Brute Force notwendig.

So macht der bcrypt[^bcrypt]-Algorithmus aus dem potenziellen Passwort "ichliebeinformatik" den Hash "$2y$12$GHd0/w.IhiTRPvinGgjACO0ov4QlCskIWbK1ikvh0LJu4vcTvrF82".
Dieser Hash kann nun in der Datenbank gespeichert werden.

Meldet sich ein Nutzer an, so wird das angegebene Passwort durch den selben Hashing-Algorithmus geschickt und mit dem Eintrag in der Datenbank verglichen.
Ist der Hash identisch, so ist auch das Passwort korrekt und der Nutzer hat sich erfolgreich authentifiziert.

Es gibt viele Hashing-Algorithmen.
Einige sind besonders für Passwörter geeignet, zum Beispiel bcrypt oder Argon2.
Diese sind absichtlich langsam, um Brute-Force-Attacken zu erschweren.

[^bcrypt]: [Bcrypt algorithm; Nils Provos, David Mazières: "A Future-Adaptable Password Scheme"](https://www.usenix.org/legacy/event/usenix99/provos/provos.pdf)

## TL;DR

Die sieben Grundprinzipien des Datenschutzes besagen: Informiere deine Nutzer, was du speicherst, speichere nur so viel und lange wie nötig und verwende Daten nur für ihren ursprünglichen Zweck.
Möchte man Passwörter speichern, so sollte man sie vorher **immer** hashen, zum Beispiel mit bcrypt.

