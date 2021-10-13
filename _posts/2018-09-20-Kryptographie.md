---
layout: post
title: "Kryptographie - bmljaHQgdmVyc2NobMO8c3NlbHQ="
categories:
  - Lernblog
  - Java
lang: de-DE
---

Im letzten Jahr wurde im Unterricht schon ein [kleiner Einstieg](https://simonknott.de/articles/verschluesselung) in die Kryptographie gewagt.
Nun wird diese Reihe fortgesetzt und um aktuelle Verfahren wie den Diffie-Hellman-Schlüsselaustausch und das RSA-Verfahren erweitert.

<!--more-->

Damals wurden [Caesar](https://en.wikipedia.org/wiki/Caesar_cipher) und [Vigenère](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher) implementiert - zwei einfache Verfahren, die auf Verschiebung der Daten um einen festgelegten Schlüssel basieren.

Caesar verschiebt jeden Buchstaben um die gleiche, festgelegte Zahl.
Der Klartext "Anna" wird mit {% latex %}3{% endlatex %} als Schlüssel zum Chiffre "Dqqd", aus dem durch das Rückverschieben um {% latex %}3{% endlatex %} wieder "Anna" entsteht.

Vigenère erweitert dies um einen variierenden Schlüssel.
"abc" als Schlüssel bedeutet: Verschiebe den ersten Buchstaben um {% latex %}1{% endlatex %}, den zweiten um {% latex %}2{% endlatex %}, den dritten um {% latex %}3{% endlatex %} und den vierten wieder um {% latex %}1{% endlatex %} (und so weiter...).

Man könnte nun denken: "Na also, dann kann ich ja jetzt beruhigt meine Online-Überweisung tätigen - Vigenère verschlüsselt das ja sicher."
Dieser Gedanke wäre fatal, denn sowohl Cäsar als auch Vigenère sind angreifbar:

Caesar ist einfach über Brute-Force anzugreifen, schließlich gibt es nur 26 Möglichkeiten der Verschiebung.
Vigenère kann über eine Häufigkeitsanalsye angegreifen werden - das funktioniert aber nur, wenn der Schlüssel zu kurz ist - diese Eigenschaft wird später noch wichtig.

## Diffie-Hellman-Schlüsselaustausch

Nehmen wir an, zwei Personen möchten miteinander kommunizieren.
Diese einigen sich darauf, Vigenère mit ausreichend langem Schlüssel zu verwenden.
Nun müssen sie diesen Schlüssel irgendwie aushandeln - am besten, ohne sich dabei zu treffen.
Der Diffie-Hellman-Schlüsselaustausch ermöglicht dies.

> Zum besseren Verständnis möchte ich drei Figuren einführen:
> _**A**lice_, _**B**ob_ und _**E**ve_ ("Eavesdropper").
> Alice möchte Bob eine Nachricht senden, ohne dass Eve diese mitlesen kann.
> Der Haken ist: Alice und Bob können *ausschließlich* über Eve kommunizieren.
> Eve kann das, was sie in den Händen hält, mitlesen und sogar manipulieren.
> 
> Ein Kryptographisches Verfahren muss also leisten, dass Alice und Bob kommunizieren können, ohne dass Eve diese Kommunikation mitlesen kann.

Zur Aushandlung des Schlüssels wird zuerst ein öffentlicher Schlüssel {% latex %}(p, g){% endlatex %} bestimmt.
Dieser wird nach folgenden Bestimmungen gewählt:

- {% latex %}p{% endlatex %} ist eine Primzahl.
- {% latex %}g < p ; g \in \mathbb{R}{% endlatex %}

Dieses Schlüsselpaar wird nun veröffentlicht - Alice, Bob und Eve kennen es.

Nun wählen Alice und Bob im Geheimen einen eigenen, privaten Schlüssel:

{% latex centred %}
a < p \quad \textrm{bzw.} \quad  b < p
{% endlatex %}

Damit bestimmen sie ihre öffentlichen Austausch-Werte:

{% latex centred %}
A = g^a \bmod p
{% endlatex %}
{% latex centred %}
B = g^b \bmod p
{% endlatex %}

Diese werden dann wieder veröffentlicht, Alice erhält also {% latex %}B{% endlatex %} und Bob erhält {% latex %}A{% endlatex %}.

Aus diesen Werten kann nun einfach ein gemeinsamer Schlüssel bestimmt werden:

{% latex centred %}
K_A = B^a \bmod p
{% endlatex %}{% latex centred %}
K_B = A^b \bmod p
{% endlatex %}

Dass {% latex %}K_A{% endlatex %} mit {% latex %}K_B{% endlatex %} identisch ist, lässt sich mathematisch beweisen.
Mehr Informationen dazu finden sich [in der zugehörigen Veröffentlichung](https://ee.stanford.edu/~hellman/publications/24.pdf) .

Eve hat zwar während des Schlüsselaustauschs alle Informationen übertragen, kennt aber nur {% latex %}p{% endlatex %}, {% latex %}g{% endlatex %}, {% latex %}A{% endlatex %} und {% latex %}B{% endlatex %}.
Daraus kann Eve nicht auf K schließen, denn ihr fehlt einer der geheimen Schlüssel {% latex %}a{% endlatex %} und {% latex %}b{% endlatex %}.
Trotzdem verfügen Alice und Bob nach diesem Schlüsselaustausch über einen gemeinsamen Schlüssel, mit dem sie nun ihre Daten verschlüsseln können.
Jetzt könnte zum Beispiel Vigènere oder One-Time-Pad[^otp] eingesetzt werden - bei hohen Schlüssellängen sind diese Verfahren nämlich sicher[^sicher].

[^otp]: [One-Time-Pad](https://en.wikipedia.org/wiki/One-time_pad): Bitweise XOR-Verknüpfung von Daten und Schlüssel

[^sicher]: "sicher" bedeutet, dass sie nur durch eine Brute-Force-Attacke geknackt werden können.

Dort liegt eine Krux des Verfahrens: Um einen großen Schlüssel zu erzeugen, müssen große Eingabewerte gewählt werden - deshalb sind große Primzahlen [für die Kryptographie so wichtig](https://stackoverflow.com/questions/439870/why-are-primes-important-in-cryptography).

DH kann angegriffen werden, wenn zu kleine Werte verwendet werden, oder {% latex %}r{% endlatex %} unklug gesetzt wird.
{% latex %}r{% endlatex %} sollte ein [Generator](https://en.wikipedia.org/wiki/Generator_(mathematics)) sein, sonst könnte Eve {% latex %}A{% endlatex %} und {% latex %}B{% endlatex %} auf {% latex %}a{% endlatex %} oder {% latex %}b{% endlatex %} schließen.

Eine Beispiel-Implementierung für Diffie-Hellman findet sich [in meinem GitHub-Repository](https://github.com/Skn0tt/lkNetzwerke/tree/master/KeyExchange).

## Asymmetrische Verschlüsselungsverfahren

Verschlüsselungsverfahren, wie wir sie bisher kennen gelernt haben, verwenden zum *Ver*schlüsseln den selben Schlüssel wie zum *Ent*schlüsseln.
In Kontrast zu diesen *symmetrischen* Verfahren stehen solche, die zum *Ver*schlüsseln und *Ent*schlüsseln zwei separate Schlüssel verwenden.
Solche Verfahren nett man *asymmetrische*, ein Beispiel ist das als sicher geltende RSA.

### RSA

RSA wurde 1977 von den drei Wissenschaftlern [Ron Rivest](https://en.wikipedia.org/wiki/Ron_Rivest), [Adi Shamir](https://en.wikipedia.org/wiki/Adi_Shamir) und [Leonard Adleman](https://en.wikipedia.org/wiki/Leonard_Adleman) vorgestellt.

Um RSA verwenden zu können, muss erst ein Schlüsselpaar, also ein *öffentlicher* und ein *privater* Schlüssel, erzeugt werden.

Dafür werden zu Beginn zwei beliebige Primzahlen {% latex %}p{% endlatex %} und {% latex %}q{% endlatex %} gewählt.

{% latex centred %}
p \in \mathbb{P} {% endlatex %}{% latex centred %}
q \in \mathbb{P}
{% endlatex %}

Aus diesen kann dann {% latex %}N{% endlatex %} und {% latex %}r{% endlatex %} ermittelt werden:

{% latex centred %}
N = p * q
{% endlatex %}
{% latex centred %}
r = \phi(p, q) = (p - 1) * (q - 1)
{% endlatex %}

Nun wird eine weitere, beliebige Zahl {% latex %}e{% endlatex %} gewählt, die mit {% latex %}r{% endlatex %} Teilerfremd ist.

{% latex centred %}
e \in \mathbb{N}
{% endlatex %}
{% latex centred %}
ggt(e, r) = 1
{% endlatex %}

Als letzte Berechnung muss nun noch {% latex %}d{% endlatex %}, das *modulare Inverse* von {% latex %}e{% endlatex %} und {% latex %}r{% endlatex %} gefunden werden.

{% latex centred %}
e * d \bmod r = 1
{% endlatex %}

Nun sind alle Teile des Schlüsselpaars vorhanden:
Der *öffentliche* Schlüssel {% latex %}(N, e){% endlatex %} und der *private* Schlüssel {% latex %}(N, d){% endlatex %}.
Die anderen berechneten Werte sollten zerstört werden, um die Rekonstruktion des privaten Schlüssels zu verhindern.

Möchte man nun eine Nachricht {% latex %}m{% endlatex %} verschlüsseln, so verwendet man den *öffentlichen* Schlüssel:

{% latex centred %}
c = m^e \bmod N
{% endlatex %}

Um dieses Chiffre {% latex %}c{% endlatex %} nun wieder zu entschlüsseln, kommt der *private* Schlüssel zum Einsatz:

{% latex centred %}
m = c^d \bmod N
{% endlatex %}

Um das Erzeugen eines Schlüssels einmal selbst auszuprobieren, kann ich die [Website der Drexel University](https://www.cs.drexel.edu/~jpopyack/IntroCS/HW/RSAWorksheet.html) sehr empfehlen.

## Kryptographie in Java

Kryptographische Verfahren basieren oft auf der Verwendung großer Zahlen, die gerne die Größe von 32 Bit (Java's `int`-Typ) übersteigen.

Um diese Begrenzung zu umgehen, stellt das JDK die `BigInteger`-Klasse zur Verfügung.
Sie hält beliebig große Zahlen, ihre einzige Grenze ist die Größe des Arbeitsspeichers.

Sie verfügt außerdem schon über alle Rechenarten, die wir für die meisten Verschlüsselungsverfahren benötigen!

Als Beispiel möchte ich die Implementation von *RSA* anführen.

Das Ver- und Ent-schlüsseln verwendet die `a.modPow(e, m)`-Methode des BigInteger, die dem Term {% latex %}a^e \bmod m{% endlatex %} entspricht.

```java
BigInteger encrypt(BigInteger m, BigInteger e, BigInteger N) {
  return m.modPow(e, N);
}

BigInteger decrypt(BigInteger c, BigInteger d, BigInteger N) {
  return c.modPow(d, N);
}
```

Das Erzeugen eines Schlüsselpaars ist ähnlich simpel:

```java
Random random = new SecureRandom();
BigInteger p = BigInteger.probablePrime(1024, random);
BigInteger g = BigInteger.probablePrime(1024, random);

BigInteger N = p.multiply(g);
BigInteger r = p.subtract(BigInteger.ONE)
                .multiply(
                  g.subtract(BigInteger.ONE)
                );

BigInteger e = computeE(r);

BigInteger d = e.modInverse(r);
```

`computeE(r)` ist eine Methode, die so lange eine zufällige Zahl {% latex %}e{% endlatex %} erzeugt, bis sie alle drei Bedingungen erfüllt:

1. {% latex %}e >= 1{% endlatex %}
2. {% latex %}e <= r{% endlatex %}
2. {% latex %}\gcd{e, r} = 1{% endlatex %}

```java
static BigInteger computeE(BigInteger r) {
  BigInteger e;

  do e = new BigInteger(r.bitLength(), random);
  while (e.compareTo(BigInteger.ONE) <= 0 // 1
          || e.compareTo(r) >= 0 // 2
          || !e.gcd(r).equals(BigInteger.ONE)); // 3

  return e;
}
```

## TL;DR

*Diffie-Hellman* ermöglicht es, sich auf einen gemeinsamen Schlüssel zu eignen, ohne dass ein Dritter diesen erfährt.
Dabei werden *große Primzahlen* und eine geschickte Wahl des Generators {% latex %}g{% endlatex %} benötigt.

Asymmetrische Verschlüsselungsverfahren haben einen *öffentlichen* Schlüssel, mit dem Daten *ver*schlüsselt werden, sowie einen *privaten* Schlüssel, mit den diese wieder *ent*schlüsselt werden können.
Ein Beispiel eines solchen Verfahrens ist RSA.

Möchte man eines dieser Verfahren in Java implementieren, so muss man oft noch nicht einmal komplizierte Algorithmen umsetzen - die Klasse `BigInteger` kann die meisten Rechenarten von Haus aus.
