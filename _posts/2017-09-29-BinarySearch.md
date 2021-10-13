---
layout: post
title: Binäre Suche
categories:
  - Lernblog
  - Algorithms
description: Schnelle Suche mithilfe der Rekursion
lang: de
comments: true

---
In einer sortierten Menge ist eines der schnellsten Suchverfahren die *Binäre Suche*: Ihre Laufzeit beträgt {% latex %} O(log(n)) {% endlatex %}.

Es funktioniert wie folgt:  
Ich betrachte den Mittelwert der Menge.  
Ist er der gesuchte Wert, bin ich fertig und habe meinen Wert gefunden.  
Ist er das nicht, vergleiche ich diesen Wert mit dem gesuchten.  
Ist er kleiner, so führe ich diesen Algorithmus für den Bereich links der Mitte aus.  
Ist er größer, so führe ich diesen Algorithmus für den Bereich rechts der Mitte aus.  
Enthält mein Suchbereich nur noch einen Wert, so prüfe ich, ob dieser der gesuchte Wert ist.  
Ist er das nicht, dann ist der gesuchte Wert auch nicht in der Menge enthalten.  

![Animation Binary Search](https://upload.wikimedia.org/wikipedia/commons/9/9b/Binary_search_tree_example.gif "Animation Binary Search")

In dieser Animation kann man die Funktionsweise gut erkennen.

<!--more-->

### Pseudocode
```
suche(gesWert, links, rechts)
  wenn Array nur 1 Wert hat
    wenn Wert gefunden dann gib Index zurück
    sonst gib -1 zurück
  sonst
    wenn mittlerer Wert richtig ist
      dann gib Index des mittleren Werts zurück
    sonst
      wenn gesuchter Wert kleiner ist
        suche(gesWert, links, mitte - 1)
      sonst
        suche(gesWert, mitte + 1, rechts)
```

### Implementierung
```java
int binSuche(int gesucht, int links, int rechts) {
  if (links == rechts) {
    if (array[links] == gesucht) return links;
    else return -1;
  }

  int mitte = (links + rechts) / 2;
  int mitteWert = array[mitte];

  if (mitteWert == gesucht) return mitte;
  if (mitteWert > gesucht) return binSuche(gesucht, links, mitte);

  return binSuche(gesucht, mitte + 1, rechts);
}
```

### Laufzeit
Zu Beginn erwähnte ich, dass die Binäre Suche die Laufzeit {% latex %}O(log(n)){% endlatex %} hat.
Wieso ist das so?

Mit jedem Suchschritt verdoppele ich die Anzahl der Werte, die ich ausschließen kann.
Damit beträgt Anzahl der Werte, die nach {% latex %}n{% endlatex %} Schritten durchsucht wurden {% latex %}2^n{% endlatex %} - nach {% latex %}n{% endlatex %} umgestellt ergibt sich die Laufzeit {% latex %}O(log(n)){% endlatex %}.


### Quellcode
Mein Quellcode ist einsehbar unter [github.com/Skn0tt/lkAlgorithmik/tree/master/BinarySearch](https://github.com/Skn0tt/lkAlgorithmik/tree/master/BinarySearch)
