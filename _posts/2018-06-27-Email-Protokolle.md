---
layout: post
title: Email-Protokolle
categories:
  - Lernblog
  - Network
lang: de
published: true
---
Im [vorherigen Post](https://simonknott.de/articles/KommunikationsProtokolle) wurde beschrieben, wofür Protokolle benötigt werden, außerdem wurde das Echo-Protokoll kurz vorgestellt und implementiert.
Das Echo-Protokoll ist im Produktiveinsatz nur beschränkt nützlich - deshalb soll es in diesem Post um zwei Protokolle gehen, die noch heute in reger Benutzung sind: POP und SMTP.

<!--more-->

## POP - Post Office Protocol

POP ist durch [RFC 1939](https://www.ietf.org/rfc/rfc1939.txt) definiert und wird von EMail-Clients verwendet, um beim EMail-Server den Posteingang abzufragen.
Es wird heute nur noch selten benutzt, da das Konkurrenzprotokoll *IMAP* viele Vorteile (Synchronisation, Notizen etc.) bietet.

Möchte man POP von Hand ausprobieren, so kann man unter Windows dafür Putty verwenden, unter Unix kann dafür `nc` *(netcat)* verwendet werden.
Der Standard-POP-Port ist 110.

```
$ nc mail.simonknott.de 110

+OK Dovecot ready
```

Verbindet man sich mit einen POP-Server, wird er mit "Dovecot ready" antworten, [Dovecot](https://www.dovecot.org/) ist übrigens ein Open-Source-Email-Server.

Nun führt man mit den Befehlen `USER` und `PASS` den Anmeldevorgang durch:

```
USER test@simonknott.de
+OK
PASS meingeheimspasswort1!
+OK Logged in.
```
Ist man authentifiziert, kann man weitere Befehle benutzen.
Mit `STAT` lassen sich allgemeine Informationen abfragen:

```
STAT
+OK 3 41231
```
Bedeutet: Es liegen drei Nachrichten mit einer Gesamtgröße von 41231 Bytes vor.
Mit `LIST` werden diese genauer aufgelistet:

```
LIST
+OK 3 messages:
1 8912
2 3178
3 32319
```

So können die einzelnen Nachrichtengrößen betrachtet werden.
Möchte man nun eine der Nachrichten abrufen, wird der `RETR` (Retrieve) benutzt:

```
RETR 2
+OK 3178 octets
Return-Path: <mailings@gmx.net>
X-Flags: 1001
... usw. ...
blablabla
```

Der wichtigeste Befehl:
Mit `QUIT` kann die Sitzung geschlossen werden.

## SMTP - Simple Mail Transfer Protocol

SMTP wurde in [RFC 821](https://tools.ietf.org/html/rfc821) definiert und ist das Protokoll, mit dem Mails verschickt werden.
Es ist auch heute das Standardprotokoll für den Mailverkehr.

Der SMTP-Standard-Port ist die 25, manchmal wird auch die 587 verwendet.

```
nc smtp.simonknott.de:25

220-uxs-bsd-240-023.unaxus.net ESMTP Exim 4.72
```
Der Server begrüßt den Client.

```
EHLO test@simonknott.de
250-uxs-bsd-240-023.unaxus.net Hello ip-95-223-251-90.unitymediagroup.de 250-8BITMIME
... usw. ...
250 STARTTLS
```

Mit `EHLO` und unsere Email-Addresse grüßt der Client zurück.
Der Server wird dann mit weiteren Informationen antworten, diese Enden mit den möglichen Authentifizierungsschemata.

Wir verwenden `AUTH LOGIN`:

```
AUTH LOGIN
334 VXNlcm5hbWU6
```

Der Server antwortet ein wenig kryptisch:
`VXNlcm5hbWU6` ist `Username:` als Base64 kodiert.

Darauf antworten wir mit unserem Nutzernamen, ebenfalls als Base64 kodiert.

```
334 VXNlcm5hbWU6
dGVzdEBzaW1vbmtub3R0LmRl
334 UGFzc3dvcmQ6
bWVpbmdlaGVpbWVzcGFzc3dvcnQxIQ==
235 Authentication succeeded
```
Der Server fragt dann nach dem Passwort (`UGFzc3dvcmQ6` bedeutet `Password:`), welches wir ihm als Antwort übermitteln.

Sind die Daten korrekt, wird der Server mit `Authentication succeeded` bestätigen.

> Base64 ist nichts anderes als die Übersetzung der ASCII-Codes in die 64er-Basis, so können viele Daten gespart werden.  
> Für das Kodieren in Base64 bietet sich die Verwendung von Websites wie [base64encode.org](https://www.base64encode.org/) an.  
> Alternativ kann das Kommandozeilen-Tool `base64` (`echo test@simonknott.de | base64`) oder die Browser-Konsole (`btoa("test@simonknott.de")`) verwendet werden.

Nun kann man beginnen, eine neue Mail zu schreiben:

```
MAIL FROM: test@simonknott.de
250 ok
RCPT TO: contact@eyyupalkis.dev
250 Accepted
```

Mit `DATA` beginnt man den Brief-Körper, mit einem Punkt beendet man ihn und versendet die Nachricht.

```
DATA
354 Enter message, ending with “.”

From: test@simonknott.de
To: contact@eyyupalkis.dev
Subject: Test-Email
Date: Tue, 26 June 2018 11:16:42

Hey Simon,
Hast du schon von diesem Twitter-User @skn0tt gehört?
Ganz komischer Kautz - Dem musst du unbedingt folgen!
.
```

Beim Tippen der Nachricht sollte man sich beeilen, viele Server schließen nach sechzig Sekunden die Verbindung.

Möchte man einen solchen SMTP-Client implementieren, gilt es eine Problematik zu beachten:  
Da man auf die Antwort des Servers warten muss, muss hier mit asynchronem Code gearbeitet werden.
Verwendet man die Netzwerkklassen des Landes NRW, so kann man nach jeder gesendeten Nachricht `connection.receive()` ausführen - diese Methode blockiert den Thread so lange, bis eine Antwort vom Server kam.

### SMTP-Exploits

Das SMT-Protokoll spezifiziert nur die Interaktionen vor der Verwendung von `DATA`, der eigentliche Mail-Body ist also nicht spezifiziert.  
Tatsächlich sind also die Kopfzeilen (`From: `, `To: ` etc...) nicht notwendig, sondern eine Konvention der Mail-Systeme - dementsprechend überprüft der SMTP-Server diese auch nicht.
Dort kann man also beliebige Werte eintragen, und so auch fremde Email-Addressen als Absender spoofen oder andere Absende-Daten eintragen.

Aber: Viele Spamfilter erkennen, wenn eine Email ein unpassendes Datum enthält oder die Addresse des Absenders nicht mit der in der Nachricht übereinstimmt - man ist diesen Exploits also nicht ganz schutzlos ausgeliefert.

Es kann sehr aufschlußreich sein, sich in seinem Email-Client einmal den Quelltext anzuschauen.
Darin sind alle Server, über die diese Mail geschickt wurde, aufgeführt, außerdem kann der gesamte Envelope der Mail eingesehen werden.

## TL;DR

POP und SMTP sind zwei simple Protokolle, mit denen man Mails abrufen und verschicken kann.

Besonders SMTP hat allerdings große Sicherheitslücken, durch die Emails einfach manipuliert werden können.
