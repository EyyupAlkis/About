---
layout: post
title: Damenproblem
categories:
- Lernblog
- Recursion
description: Das 8-Damen-Problem
lang: de
comments: true

---

Das Damen-Problem spielt sich auf einem Schachfeld der Abmessungen {% latex %}N*N{% endlatex %} ab.
Wir möchten auf diesem Schachfeld {% latex %}N{% endlatex %} Damen so platzieren, dass keine Bedrohung vorliegt.

<img alt="Beispiel 4 Damen" title="Beispiel 4 Damen" src="../assets/legacy_gs_bucket/Damenproblem.svg" height="200" width="200">
<!--more-->

Die Dame ist die stärkste Figur im Schach.
Sie kann sich horizontal, vertikal und diagonal beliebig bewegen.

### Lösungsansatz
Wir wissen: In jeder Zeile und jeder Spalte muss genau {% latex %}1{% endlatex %} Dame sein - sonst bedrohen sie sich gegenseitig.

Um das Problem zu lösen, eignet sich folgender Algorithmus:
Ich betrachte die erste Zeile und finde alle Möglichkeiten, an denen ich eine Dame aufstellen kann.
Ich stelle eine Dame an der ersten Möglichkeit auf.
Nun tue ich das gleiche für die nächste Zeile.
Gibt es darin keine Möglichkeiten mehr, so gehe ich eine Zeile zurück, nehme die Dame wieder weg und wiederhole den Algorithmus für die nächste Möglichkeit.

Animiert sähe dieser Algorithmus so aus:  
![Animation Backtracking](https://upload.wikimedia.org/wikipedia/commons/1/1f/Eight-queens-animation.gif "Animation Backtrack")

#### Pseudocode
```
funktion backtrack
    betrachte alle möglichkeiten in der zeile
      
    für jede möglichkeit
        setzte dame auf Möglichkeit
        führe backtrack für nächste Zeile aus
        wenn Lösung gefunden
            dann gib Lösung zurück
        nimm dame wieder weg
        
    gib keine lösung zurück
```

#### Implementierung
```java
Pos[] backtrack(int row) {
    // Ermittele alle Möglichen Standorte einer Dame in der betrachteten Zeile
    Pos[] moeglichkeiten = getMoeglichkeiten(row);

    for (Pos p : moeglichkeiten) {
      setzeDame(p);

      Pos[] loesung = backtrack(row + 1);
      if (loesung != null) return loesung; // Lösung gefunden

      entferneDame(p);
    }

    return null; // Keine Lösung gefunden
  }
```

#### Laufzeit
Ich betrachte für {% latex %}N{% endlatex %} Reihen {% latex %}N{% endlatex %} Möglichkeiten, die Laufzeit ist demnach:

{% latex centred %}
O(n^2)
{% endlatex %}