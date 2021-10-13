---
layout: post
title: Kommunikationsprotokolle
categories:
  - Lernblog
  - Network
lang: de
published: true
---

*Netzwerke* und *Netzwerkkommunikation* sind das neue Thema des Informatik-LKs - wir beschäftigen uns damit, wie verteilte Systeme miteinander kommunizieren und wie man diese selbst entwickelt.
Damit zwei Systeme überhaupt Daten zwischen einander übertragen können, muss es ein vereinbartes Kommunikationsprotokoll geben.
Was so ein Protokoll ist, was es können muss, und wie man es fehlerresistent gestaltet, wird in diesem Post beschrieben.

<!--more-->

## Was ist ein Protokoll?

Ein Protokoll ist ein Satz festgelegter Regeln, die beide Kommunikationspartner beachten.
Protokolle sind nicht nur in der Informatik wichtig, sie lassen sich auch in der Sprache, zum Beispiel in der Armee oder in der Fliegersprache finden.  
Fälle aus dem Alltag könnten sein:

**Begrüßung**: "Hallo Hans!" - "Hallo Peter!" Jetzt weiss Hans, dass Peter da ist.  
**Verabschiedung**: "Tschüss Hans!" - "Tschüss Peter!" Jetzt weiss Hans, dass Peter *nicht mehr* da ist.  

Je kleiner der Spielraum für Fehler sein soll, desto formaler und genauer muss auch das Protokoll definiert sein - besonders für die Anwendung in der Informatik.
So können diese gerne mehrere hundert Seiten umfassen: Das HTTP-Protokoll, welches zum Beispiel im Internet zur verwendet wird, wird in [RFC 2616](https://tools.ietf.org/html/rfc2616) definiert und umfasst 176 Seiten.

Ein simples Protkoll ist das Echo-Protkoll:

> Wenn der Client dem Server eine Nachricht schickt, schickt der Server diese mit Auslassungspunkten versehen zurück

```
Hallo
> Hallo...
Huhuhuhu
> Huhuhuhu...
```

Für dieses Protokoll ist am Ende des Posts eine Implementierung zu finden.

Die Protokolle POP und SMTP, die zum Abrufen und Versenden von EMails verwendet wurden und teilweise noch heute in Benutzung sind, werden im [nächsten Blog-Eintrag](https://simonknott.de/articles/Email-Protokolle) erläutert.

## Zwingend benötigte Befehle

Damit eine Kommunikation zustande kommen kann, muss es einen `START`-Befehl geben: "Hallo Gerd, ich bin jetzt da."
Genauso muss es natürlich einen `ENDE`-Befehle geben: "Tschüss Gustav, bin jetzt weg!"

Außerdem muss es einen `OK`-Befehl geben: "OK Gerd, das sehe ich genau so.".
Auch ein `FEHLER`-Statement darf nicht fehlen: "Gustav, die Musik ist zu laut. Kannst du das nochmal wiederholen?"

Diese Kontrollsequenzen werden auf jeden Fall benötigt, damit es keine Verständnisprobleme gibt.

## Datenvalidierung

Bei der Datenübertragung können immer wieder Fehler passieren - eine fehlherhafte Abschirmung des Netzwerkkabels kann Störungen ermöglichen, im WLAN können ganze Pakete verloren gehen.
Damit trotzdem sicher gestellt werden kann, dass die übertragenen Daten korrekt sind, gibt es verschiedene Validierungsverfahren.
Diese arbeiten alle nach dem selben Prinzip: Aus den übertragenen Daten wird ein abstrakter Prüfwert generiert, der dann vom Empfänger mit dem selben Verfahren überprüft wird.

### Paritätsbit

Beim Paritätsbit wird an die Daten genau ein Bit angehängt.  
Dieses ist 1, wenn die Daten eine gerade Anzahl von positiven Bits enthält, und 0, wenn die Anzahl negativ ist.

Beispiel:

`10101 1` (3 Einsen &rarr; Ungerade &rarr; `1`)  
`10001 0` (2 Einsen &rarr; Gerade &rarr; `0`)

Dieses Verfahren erkennt weder getauschte Bits (`10` hat so viele Einsen wie `01`) noch mehrere Bitflips (`010` hat so viele Einsen wie `001`).

### Prüfsumme

Hier wird die Anzahl der positiven Bits mit einer Zahl {% latex %}n{% endlatex %} modulo genommen und dann angehängt.

Beispiel für {% latex %}n = 4{% endlatex %}:

`111111 10` ({% latex %}6 \mod 4 = 2{% endlatex %})  
`101101 00` ({% latex %}4 \mod 4 = 0{% endlatex %})

Dieses Verfahren erkennt getauschte Bits nicht ({% latex %}001 \mod 4 \equiv 010 \mod 4{% endlatex %}).
Einen Bitflip erkennt auch dieses Verfahren nicht, da `010` so viele Einsen hat wie `001`.

### Gewichtete Prüfsumme

Um einen Bitflip zu erkennen, müssen die einzelnen Bits unterschieden werden:

Jedem Bit wird eine Gewichtung zugeteilt, mit der es multipliziert wird.
Aus dem resultierenden Wert wird dann analog zur Prüfsumme die *gewichtete Prüfsumme* bestimmt.

Beispiel für Gewichtung {% latex %}1, 2, 3{% endlatex %} und {% latex %}n = 4{% endlatex %}:

| Beispiel  | Erklärung                            |
| --------- | ------------------------------------ |
| {% latex %}101\ 00{% endlatex %} | {% latex %}(1 * 1 + 0 * 2 + 1 * 3) \mod 4 = 0{% endlatex %} |
| {% latex %}001\ 11{% endlatex %} | {% latex %}(0 * 1 + 0 * 2 + 1 * 3) \mod 4 = 3{% endlatex %} |

Dieses Verfahren erkennt *einen* Bitflip gut, aber nicht immer *mehrere*:

{% latex %}001\ 11 = 110\ 11{% endlatex %}

Bei diesem Beispiel ist die Lücke, dass {% latex %}1 + 2 = 3{% endlatex %} ist: Die *Gewichtete Quersumme* hat für zwei verschieden Kombinationen das selbe Ergebnis.
Dieser konkrete Fehler kann vermieden werden, indem zum Beispiel die Zweierpotenzen als Gewichtung eingesetzt werden.

### Jedes Prüfverfahren ist fehlerbehaftet!

*Paritätsbit* und (gewichtete) *Prüfsumme* können zwar einige Fehler ausschließen, haben aber Lücken, durch die Fehler geschehen können.
Der Grund ist simpel: Aus einer fehler*freien* Nachricht kann durch Bitflips und -swaps eine neue, fehler*behaftete* Nachricht entstehen, in der auch die Prüfinformationen zufällig wieder zur Nachricht passen.

Deshalb gilt:  
> Ob eine Nachricht fehlerfrei ist, lässt sich nie ohne Zweifel sagen.
> Wenn durch eines der Prüfverfahren ein Fehler erkannt wird, ist er sicher ein Fehler.

Die Fehleranfälligkeit lässt sich jedoch durch die Auswahl und Kombination der richtigen Prüfverfahren und die Validierung großer Datenmengen (zum Beispiel mit dem SHA-256-Algorithmus) so weit reduzieren, dass korrupte Datenübertragung kein großes Problem mehr darstellt.

## Implementierung des Echo-Protokolls

Wie zu jedem Thema des Leistungskurses gibt es auch zur Implementierung von Netzwerkanwendungen eine Reihe an Hilfsklassen der Landesregierung NRW.
Mithilfe dieser wird nun das oben beschriebene Echo-Protokoll implementiert.

### EchoServer

Der *EchoServer* erweitert die *Server*-Klasse.

```java
class EchoServer extends Server {
  EchoServer(int port) {
    super(port);
  }
}
```
Deren Konstruktor erhält den Netzwerk-Port, auf dem der Server geöffnet werden soll.

```java
void processNewConnection(String clientIp, int clientPort) {
  send(clientIp, clientPort, "Du bist angenommen. Hallo!");
}
```

Wenn ein neuer Client sich verbindet, wird ihm eine Willkommensnachricht zurückgesendet.
Dafür wird die `send`-Methode der Server-Klasse benutzt, mit der einem Client eine Nachricht geschickt werden kann.

```java
void processMessage(String clientIp, int clientPort, String msg) {
  send(clientIp, clientPort, msg + "...");
}
```

Wenn der Server vom Client eine Nachricht erhält, sendet der Server diese mit Auslassungspunkten versehen zurück.

```java
void processClosedConnection(String clientIp, int clientPort) {}
```

Es wäre noch Möglich, etwas besonderes auszuführen, wenn der Client die Verbindung schließt - dies ist aber für den EchoServer nicht notwendig.

### EchoClient

Der `EchoClient` ist das Gegenstück zum `EchoServer`: Er verbindet sich mit dem Server, sendet ihm Nachrichten und zeigt die Antworten an.

Sein Konstruktur nimmt IP und einen Port an, mit dem er sich verbinden soll.
Dann startet er den Feedbackloop.

```java
class EchoClient extends Client {
  EchoClient(String ip, int port) {
    super(ip, port);
    startFeedbackLoop();
  }
}
```

```java
Scanner s = new Scanner(System.in);
void startFeedbackLoop() {
  while (true) {
    String msg = s.nextLine();
    send(msg);
  }
}
```

Der Feedbackloop nimmt immer eine Zeile aus der Konsole entgegen und sendet diese an den Server.
Dafür benutzt er die `send`-Methode der `Client`-Klasse, diese bekommt nur die Nachricht übergeben.

```java
@Override
public void processMessage(String msg) {
  super.processMessage(msg);
  System.out.println(s);
}
```

Damit die Antworten auch angezeigt werden können, wird die `processMessage`-Methode überschrieben.
Wichtig: Möchte man eine Methode erweitern, sollte darin zuerst die überschriebene Methode aufgerufen werden.
So geht die Logik der Superklasse nicht verloren.

# TL;DR

Ein Kommunikationsprotokoll ist ein formal definierter Regelsatz, nach dem zwei Parteien, zum Beispiel Menschen oder Computer, miteinander kommunizieren.
Um die Fehler zu vermeiden, können Prüfverfahren verwendet werden - diese können die Fehleranfälligkeit allerdings nur senken, nicht vollständig eliminieren.
