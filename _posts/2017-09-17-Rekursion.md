---
layout: post
title: Rekursion
categories:
  - Lernblog
  - Algorithms
description: Rekursion als Problemlösungsstrategie
lang: de
comments: true
---

Rekursion ist eine Problemlösungsstrategie.
Ihr besonderes Merkmal ist, dass sie komplexe Probleme aufteilt und sich dann für diese Unterprobleme selbst aufruft.
Dabei besteht ein rekursiver Algorithmus immer aus zwei Teilen:

- **Rekursionsbasis:** Falls ein Problem so einfach ist, dass man es sofort lösen kann, wird das Ergebnis zurückgegeben.
Bei einer rekursive Zahlenfolge könnten das zum Beispiel die Grundwerte sein.

- **Rekursionsschritt:** Im Rekursionsschritt wird das Problem in beliebig viele Sub-Probleme unterteilt.
Diese werden dann gelöst, zusammengesetzt und zurückgegeben.

In Pseudocode könnte es also so aussehen:
```
funktion rekursiv(n) {
  wenn einfach zu lösen
    dann gib lösung zurück
  sonst
    teile Problem in Unterprobleme auf
    führe dich selbst erneut aus
    gib zusammengesetzte Ergebnisse zurück
}
```
<!--more-->
Hierbei sind Zeile 2-3 die *Rekursionsbasis*, Zeile 4-7 *Rekursionsschritt*.

### Fibonacci
Ein Standardbeispiel für die Rekursion ist die *Fibonacci*-Sequenz.
Diese ist definiert wie folgt:

![Fibonacci Formel](http://latex.codecogs.com/svg.latex?fib%28n%29%20%3D%20%5Cbegin%7Bcases%7D%20fib%28n%20-%201%29%20&plus;%20fib%28n%20-%202%29%20%26%20n%20%3E%201%20%5C%5C%201%20%26%20n%20%3D%200%20%5Clor%20n%20%3D%201%20%5Cend%7Bcases%7D "Fibonacci Formel")

Das bedeutet: Ein Element der Fibonacci-Sequenz ist immer die Summe seiner beiden Vorgänger, wobei die ersten beiden Stellen den Wert eins haben.

Fibonacci ist also Rekursion im Paradebeispiel:
Ein Problem wird gelöst, indem die Funktion sich selbst aufruft.

Die ersten zehn Elemente der Fibonacci-Folge sind:

{% latex centred %}
1; 1; 2; 3; 5; 8; 13; 21; 34; 55
{% endlatex %}

In der Java-Implementierung sieht das ganze so aus:
```java
int fib(int n) {
  if (n == 0 || n == 1) return 1; // Base Cases

  return fib(n - 1) + fib(n - 2) // Rekursionsschritt
}
```
Zeichnet man nun ein Aufrufdiagramm, so wird ein Problem der Rekursion schnell deutlich:
Der gleiche Wert wird mehrmals berechnet.

Am Aufrufdiagramm der dritten Stelle kann man das ganze schon sehen:

{% mermaid %}
graph TD
drei["fib(3)"]
zweiI["fib(2)"]
einsI["fib(1)"]
einsII["fib(1)"]
null["fib(0)"]
drei --> zweiI
drei --> einsI
zweiI --> einsII
zweiI --> null
{% endmermaid %}
`fib(1)` wird zweimal ausgeführt. Nun macht dies beim ersten Wert keinen großen Unterschied, bei höheren Werten kann dies aber sehr schnell ins Gewicht fallen - schließlich steigt die anzahl der Aufrufe mit {% latex %}2^n{% endlatex %}.
&rarr; Laufzeit: {% latex %}O(2^n){% endlatex %}

Als Lösung lässt sich Caching einsetzen:
Wenn wir einen Wert berechnen, speichern wir ihn ab - Dann müssen wir beim nächsten Aufruf der Funktion nicht wieder die ganze Rechnung von vorne machen.
Eine Implementierung in Java könnte so aussehen:
```java
List<Integer> cache = new ArrayList<>(Arrays.asList(0, 1));
int fib(int n) {
    if (cache.size() <= n) cache.add(fib(n - 1) + fib(n - 2));
    
    return cache.get(n);
}
```
In Zeile 1 wird eine `ArrayList` initialisiert, die unsere Werte zwischenspeichert.
Wird nun die Funktion `fib(n)` aufgerufen, prüft diese als erstes, ob der Wert schon darin enthalten ist.
Dafür muss sie bloß die Länge der Liste mit dem angefragten Index vergleichen.
Dies funktioniert, da an die Liste nur angehängt wird und dank der Rekursion alle Werte von klein nach groß eingetragen werden.
Ist die Länge der Liste kleiner als der angefraget Index, dann ist der Wert noch nicht berechnet und er wird angehängt (Z. 4).
Nun wird der Wert zurückgegeben (Z. 6)
So wird jeder Wert also nur ein mal berechnet, die Laufzeit ist {% latex %}\Theta(n){% endlatex %}.

### Hanoi
"Die Türme von Hanoi" ist ein Spiel des französischen Mathematikers Édouard Lucas.
Darin existieren drei gleich große Stäbe {% latex %}A{% endlatex %}, {% latex %}B{% endlatex %} und {% latex %}C{% endlatex %}.
Auf {% latex %}A{% endlatex %} steckt ein Stapel Scheiben, jede kleiner als die darunter.
Ziel des Spiels ist es, alle Scheiben von {% latex %}A{% endlatex %} auf einen der beiden anderen Stäbe zu verschieben.
Dabei ist die entscheidende Einschränkung, dass eine Scheibe nie auf einer kleinerern aufliegen darf.

#### Lösungsstrategie
![Lösungsstrategie](../assets/legacy_gs_bucket/Türme v. Hanoi Strategie.svg "Lösungsstrategie")

Der Lösungsansatz lässt sich in folgende Schritte aufteilen:
1. Alle Scheiben bis auf die unterste von {% latex %}A{% endlatex %} nach {% latex %}B{% endlatex %} verschieben ({% latex %}Z(n-1){% endlatex %})
2. Die übrige Scheibe auf {% latex %}C{% endlatex %} setzen ({% latex %}1{% endlatex %})
3. Den Stapel auf {% latex %}B{% endlatex %} nach {% latex %}C{% endlatex %} verschieben ({% latex %}Z(n-1){% endlatex %})
Die Anzahl der zu verschiebenden Scheiben ist in Klammern notiert, {% latex %}n{% endlatex %} bezeichnet die Anzahl der Scheiben im Anfangszustand.

Möchte man die Anzahl der minimal benötigten Schritte bestimmen, so lautet die Formel

{% latex centred %} Z(n) = 2 * Z(n - 1) + 1{% endlatex %}

{% latex %}2*Z(n-1){% endlatex %} stellt dabei Schritte eins und drei, {% latex %}+1{% endlatex %} Schritt zwei dar.

#### Implementierung
##### Rekursiv
Die Implementierung sieht wie folgt aus:
```java
int hanoiRec(int n) {
  if (n == 1) return 1; // Base Case

  return 2 * hanoiRec(n - 1) + 1; // Rekursionsschritt
}
```
Laufzeit: {% latex %}\Theta(2^n){% endlatex %}

Die Rekursionsbasis ist {% latex %}n=1{% endlatex %}: Wenn nur eine Scheibe verschoben werden soll, dann dauert das genau einen Zug.
Der Rekursionsschritt implementiert exakt die oben genannte Funktion.

#### Explizit
Die Anzahl der notwendigen Züge lässt sich auch explizit bestimmen:

{% latex centred %}
Z(n) = 2^n - 1
{% endlatex %}

Laufzeit: {% latex %}\Theta(n){% endlatex %}

#### Lösen des Rätsels
Wir haben nun berechnet, in wie vielen Schritten man das Rätsel lösen kann - doch wie löse ich es nun wirklich?
Dafür nutzen wir den oben erklärten Ansatz.

#### Implementierung
```java
/**
 * Solves the TowersOfHanoi problem to move the specified
 * number of disks from one pile to another.
 *
 * @param disks the number of disks to be moved
 * @param from  the number of the pile where the disks are now
 * @param to    the number of the pile to which the disks are to be moved.
 * @param spare the number of the pile that can be used as a spare.
 */
private void solve(int disks, int from, int to, int spare) {
    /**
     * Die Methode moveOne(int fromStack, int toStack) bewegt eine Scheibe vom Stapel
     * mit der Nummer fromStack zum Stapel mit der Nummer toStack
     */
    // Rekursionsbasis: Nur eine Scheibe
    if (disks == 1) {
        moveOne(from, to);
        return;
    }

    // Bewege alle oberen auf den Spare-Haufen
    solve(disks - 1, from, spare, to);

    // Bewege die untere auf den Target-Haufen
    solve(1, from, to, spare);

    // Bewege die vom Spare-Haufen auf den Target-Haufen
    solve(disks - 1, spare, to, from);
}
```

### Hofstadter Q-Sequenz
Douglas R. Hofstadters Q-Sequenz ist eine Abwandlung der Fibonacci-Folge.
In dieser wird durch die Vorgänger {% latex %}n-1{% endlatex %} und {% latex %}n-2{% endlatex %} bestimmt, aus welchen Vorgängern das Ergebnis zusammengesetzt wird.

Die Formel sieht so aus:

![Hofstadter Q-Sequenz Formel](http://latex.codecogs.com/svg.latex?Q%28n%29%20%3D%20%5Cbegin%7Bcases%7D%20Q%28n%20-%20Q%28n%20-%201%29%20&plus;%20Q%28n%20-%20Q%28n%20-%202%29%29%29%20%26%20n%20%3E%202%201%20%26%20n%20%3D%201%20%5Clor%20n%20%3D%202%20%5Cend%7Bcases%7D "Hofstadter Q-Sequenz Formel")

Die Implementation sieht so aus:
```java
int hofstadterQRec(int n) {
  if (n <= 2) return 1;
  return hofstadterQRec(n - hofstadterQRec(n - 1)) + hofstadterQRec(n - hofstadterQRec(n - 2));
}
```
In Zeile 3 ist die Rekursionsbasis notiert: Wenn {% latex %}n{% endlatex %} kleiner/gleich 2 ist, wird 1 zurückgegeben.
In allen anderen Fällen wird der Rekursionsschritt ausgeführt, nachh dem gleichen Schema wie in der Formel zu sehen ist.

#### Aufrufdiagramm
für {% latex %}Q(4){% endlatex %}
![Aufrufdiagramm](../assets/legacy_gs_bucket/HofstadterQ%20Aufrufdiagramm.svg "Aufrufdiagramm")

### Web-Implementation
Hier kannst du dir die besprochenen Sequenzen einmal genauer anschauen.
<form>
  <input id="n" name="n" type="number" min="0">

  <input type="button" id="button" value="Berechnen">

  <br>
  
  <input type="radio" name="sequenz" value="fibR" id="fibR" checked>Fibonacci
  <input type="radio" name="sequenz" value="hanoiR" id="hanoiR">Hanoi
  <input type="radio" name="sequenz" value="qR" id="qR">Hofstadter Q
  
  <br>

</form>
<p id="output"></p>

<script>
function output(n) {
  var output = document.getElementById('output')
  output.innerHTML = n
}

var cacheFib = [0, 1]
function fib(n) {
  if (cacheFib.length <= n) cacheFib.push(fib(n - 1) + fib(n - 2))
  return cacheFib[n]
}

function hanoi(n) {
  return Math.pow(2, n) - 1
}

var cacheQ = [0, 1, 1]
function q(n) {
  if (cacheQ.length <= n) cacheQ.push(q(n - q(n - 1)) + q(n - q(n - 2)))
  return cacheQ[n]
}

function calc() {
  var n = document.getElementById('n').value

  var fibV = document.getElementById('fibR').checked
  if (fibV) {
    output(fib(n))
    return
  }

  var hanoiV = document.getElementById('hanoiR').checked
  if (hanoiV) {
    output(hanoi(n))
    return
  }

  var qV = document.getElementById('qR').checked
  if (qV) {
    output(q(n))
    return
  }
}

document.getElementById('button').onclick = calc
</script>


### Quellcode
Mein Quellcode ist einsehbar unter [https://github.com/Skn0tt/lkAlgorithmik/tree/master/Rekursion](https://github.com/Skn0tt/lkAlgorithmik/tree/master/Rekursion)
