---
layout: post
title: "Endliche Automaten"
categories:
  - "Lernblog"
  - "Theoretical Computer Science"
lang: de-DE
---

In diesem Post geht es um das neue Thema des Leistungskurses: *Endliche Automaten*.
Mit ihnen kann man auf eine einfache Art komplexe Abläufe modellieren, man findet sie auch in Eingabevalidierungen oder Compilern.

{% graphviz %}
digraph finite_state_machine {
  rankdir=LR;
  size="8,5"

  node [shape = doublecircle]; q2;
  node [shape = point]; qi

  node [shape = circle];
  qi -> q0;
  q0  -> q1 [ label = "b" ];
  q0  -> q3 [ label = "a" ];
  q3  -> q3 [ label = "a, b" ];
  q1  -> q2 [ label = "b" ];
  q1  -> q4 [ label = "a" ];
  q4  -> q4 [ label = "a" ];
  q2  -> q4 [ label = "a" ];
  q2  -> q2 [ label = "b" ];
  q4  -> q2 [ label = "b" ];
}
{% endgraphviz %}

<!--more-->

## Was ist überhaupt ein Automat?

Ein Automat besteht aus *Zuständen* und *Übergängen*.
Das obige Bild nennt man einen *Übergangsgraphen*: Zustände werden als Knoten gezeichnet, Übergänge als Kanten.

Ein Automat nimmt nun eine Eingabe entgegen und überprüft, ob sie angenommen wird.
Die Eingabe nennt man *"Wort"*, wird sie angenommen, gehört sie zur *"Sprache"* des Automaten.
Damit dieser Fachterminus einfacher verständlich ist, hier ein Beispiel:

Der obige Automat hat das Eingabe-Alphabet {% latex %}\Sigma = \{a; b\}{% endlatex %}.
Dies kann man an den Beschriftungen der Übergänge ablesen.
Nun füttert man diesen Automaten mit einem *"Wort"*, in unserem Beispiel mit `baab`.
Man steigt bei {% latex %}q_0{% endlatex %} in den Automaten ein und schaut sich das erste Zeichen des Wortes an, dann folgt man dem entsprechenden Übergang.
In unserem Beispiel ist das ein `b`, der erste Übergang ist also {% latex %}q_0 \rightarrow q_1{% endlatex %}.
Dies wird für jedes Zeichen wiederholt:

{% latex centred %}
q_0
\xrightarrow{b}
q_1
\xrightarrow{a}
q_4
\xrightarrow{a}
q_4
\xrightarrow{b}
q_2
{% endlatex %}

Die Eingabe `baab` führt also in den Zustand {% latex %}q_2{% endlatex %}.
Da dieser ein Endzustand ist (erkennbar an der doppelten Umrandung), wird das Wort angenommen und gehört somit zur Sprache des Automaten.

Die Eingabe `bba` wird hingegen nicht angenommen:

{% latex centred %}
q_0
\xrightarrow{b}
q_1
\xrightarrow{b}
q_2
\xrightarrow{a}
q_4
{% endlatex %}

Sie endet in {% latex %}q4{% endlatex %}, dies ist kein Endzustand.

Hat ein Zustand keinen Übergang, der von ihm weg führt, nennt man ihn "Senke".
Landet man einmal in ihm, kommt man nicht mehr heraus.
In unserem Beispiel ist {% latex %}q3{% endlatex %} eine Senke.

## Formale Definition

Ein Übergangsgraph kann einen Automaten sehr anschaulich beschreiben, für die Implementation ist aber eine formale Definition sehr hilfreich.

Diese besteht aus fünf Teilen:

{% latex centred %}
A = (Q, \Sigma, \delta, q_0, F)
{% endlatex %}

{% latex %}Q{% endlatex %} ist die Menge aller Zustände.

{% latex centred %}
Q = \{ q_0; q_1; q_2; q_3; q_4 \}
{% endlatex %}

{% latex %}\Sigma{% endlatex %} ist das Eingabealphabet.

{% latex centred %}
\Sigma = \{ a; b \}
{% endlatex %}

{% latex %}\delta{% endlatex %} ist die Übergangsfunktion.
Sie bestimmt aus dem aktuellem Zustand und dem nächsten Zeichen den nächsten Zustand.

|{% latex %}\delta{% endlatex %}| a  | b  |
|--------|----|----|
| **q0** | q3 | q1 |
| **q1** | q2 | q4 |
| **q2** | q4 | q2 |
| **q3** | q3 | q3 |
| **q4** | q4 | q2 |

{% latex %}q_0{% endlatex %} ist der Anfangszustand.

{% latex %}F{% endlatex %} ist die Menge aller Endzustände, in denen das Wort angenommen wird.

{% latex centred %}
F = \{ q_2 \}
{% endlatex %}

Der Beispiel-Automat ist ein ***D**eterministischer* ***E**ndlicher* ***A**utomat* (DEA).

*Deterministisch* bedeutet, dass bei jedem Zustand für jede Eingabe eindeutig bestimmt ist, in welchen Zustand übergangen wird.

*Endlich* beschreibt, dass der Automat eine endliche Anzahl an Zuständen besitzt.

Es gibt auch andere Arten von Automaten, eine davon wirst du später kennenlernen.

## Implementation

Ein DEA ist vergleichsweise einfach zu implementieren.
Eine mögliche Implementation des Beispiel-DEAs kannst du hier sehen:

```java
class ExampleDEA {
  
  int BEGINNING_STATE = 0;

  int[] END_STATES = new int[]{ 2 };

  public boolean matches(String value) {
    int state = BEGINNING_STATE;

    for (char c : value.toCharArray()) {
      state = this.nextState(state, c);
    }

    // Pseudo-Code
    return END_STATES.contains(state);
  }

  private int nextState(int currentState, char c) {
    switch (currentState) {
      case 0:
        return c == 'a' ? 3 : 1;
      case 1:
        return c == 'a' ? 2 : 4;
      case 2:
        return c == 'a' ? 4 : 2;
      case 3:
        return c == 'a' ? 3 : 3;
      case 4:
        return c == 'a' ? 4 : 2;
      default:
        return -1;
    }
  }

}
```

Die Methode `nextState` modelliert die Übergangstabelle {% latex %}\delta{% endlatex %}, die Methode `matches` verwendet diese um einen Eingabewert auf seine Gültigkeit zu überprüfen.

### Nicht-Deterministische Endliche Automaten

Im Gegensatz zu DEAs ist bei Nicht-Deterministischen Endlichen Automaten (NEA) der Übergangsgraph nicht eindeutig.
Das könnte so aussehen: Bist du bei {% latex %}p{% endlatex %} und hast {% latex %}1{% endlatex %} eingelesen, kannst du entweder nach {% latex %}p{% endlatex %} oder {% latex %}q{% endlatex %} gehen.

{% graphviz %}
digraph finite_state_machine {
  rankdir=LR;
  size="8,5"

  node [shape = doublecircle]; q;
  node [shape = point]; i

  node [shape = circle];
  i -> p;
  p -> p [ label = "0, 1" ];
  p -> q [ label = "1" ];
}
{% endgraphviz %}

Dieser Automat nimmt alle Eingabewerte an, für die es mindestens einen Weg zum Zielzustand gibt.
Gültige Werte wären zum Beispiel:

{% latex centred %}
p
\xrightarrow{1}
q
{% endlatex %}

{% latex centred %}
p
\xrightarrow{1}
p
\xrightarrow{0}
p
\xrightarrow{1}
q
{% endlatex %}

Ungültige Werte wären

{% latex centred %}
p
\xrightarrow{0}
q
{% endlatex %}

{% latex centred %}
p
\xrightarrow{1}
p
\xrightarrow{0}
p
\xrightarrow{0}
p
{% endlatex %}

Dieser Automat nimmt alle Eingaben an, die auf {% latex %}1{% endlatex %} enden.

NEAs können sehr hilfreich sein, um komplexe Automaten herunter zu brechen.

Man stelle sich einen Spamfilter vor, der Emails auf das Wort "Elch" untersucht.
Mit einem NEA kann dieser sehr überschaubar dargestellt werden:

{% graphviz %}
digraph finite_state_machine {
  rankdir=LR;
  size="8,5"

  node [shape = doublecircle]; h;
  node [shape = point]; i

  node [shape = circle];
  i -> q0;
  q0 -> q0 [label = <&Sigma;>];
  q0 -> e [label = "e"];
  e -> l [label = "l"];
  l -> c [label = "c"];
  c -> h [label = "h"];
  h -> h [label = <&Sigma;>];
}
{% endgraphviz %}

Als DEA wäre dieser Automat sehr komplex:

{% graphviz %}
digraph finite_state_machine {
  rankdir=LR;
  size="8,5"

  node [shape = doublecircle]; h;
  node [shape = point]; i

  node [shape = circle];
  i -> q0;
  q0 -> q0 [label = "&Sigma;\\{e}"];
  q0 -> e [label = "e"];
  e -> q0 [label = "&Sigma;\\{l}"];
  e -> e [label = "e"];
  e -> l [label = "l"];
  l -> q0 [label = "&Sigma;\\{c,e}"];
  l -> c [label = "c"];
  l -> e [label = "e"];
  c -> q0 [label = "&Sigma;\\{h}"];
  c -> h [label = "h"];
  c -> e [label = "e"];
  h -> h [label = <&Sigma;>];
}
{% endgraphviz %}

Interessant ist: Jeder DEA lässt sich als NEA darstellen und umgekehrt.
Ein NEA ist also nur eine einfachere Darstellung.

Jeder DEA ist schon ein NEA, eine Überführung in die andere Richtung kann man mithilfe der [Potenzmengenkonstruktion](https://de.wikipedia.org/wiki/Potenzmengenkonstruktion) durchführen.

## Reguläre Ausdrücke

Die Darstellung solcher Automaten in Übergangsgraphen ist anschaulich, aber oft zu sperrig.
Damit man Automaten dem Computer gut beibringen kann, gibt es Reguläre Ausdrücke.

{% latex centred %}
L = \{ w \in \Sigma^* \mid w = ab(a+b)^*b+ \}
{% endlatex %}

Dieser Reguläre Ausdruck besagt:
Meine Sprache sind alle Wörter {% latex %}w{% endlatex %} aus {% latex %}\Sigma^*{% endlatex %} (Menge aller Wörter, die man aus dem Alphabet {% latex %}\Sigma{% endlatex %} bilden kann), für die gilt:

1. Es beginnt mit {% latex %}ab{% endlatex %}
2. Es folgt {% latex %}0{% endlatex %} oder öfter mal {% latex %}a{% endlatex %} oder {% latex %}b{% endlatex %}
3. Es folgt mindestens ein mal {% latex %}b{% endlatex %}

Es gibt vier grundlegende Operatoren:

{% latex %}a?{% endlatex %}: Einmal oder keinmal {% latex %}a{% endlatex %}  
{% latex %}a^*{% endlatex %}: Keinmal oder mehrmals {% latex %}a{% endlatex %}  
{% latex %}a^+{% endlatex %}: Ein oder mehrmals {% latex %}a{% endlatex %}  
{% latex %}a+b{% endlatex %}: {% latex %}a{% endlatex %} oder {% latex %}b{% endlatex %}  

Diese Operatoren können beliebig kombiniert werden.

Der Reguläre Ausdruck {% latex %}(a+..+z)^*elch(a+..+z)^*{% endlatex %} macht das selbe wie obiger NEA.

Unterstützung für Reguläre Ausdrücke, oder kurz *RegEx* (Regular Expression), ist in fast allen Sprachen schon eingebaut.

Das obige Elch-Beispiel in einigen Sprachen:

**Java:**
```java
Pattern pattern = Patter.compile(".*elch.*");
Matcher m = p.matcher("Ein Elch läuft über die Straße.");
m.matches();
```

**Javascript:**
```js
const regex = /.*elch.*/i;
regex.test("Ein Elch läuft über die Straße.");
```

**Python:**
```python
re.match(".*elch.*", "Ein Elch läuft über die Straße.");
```

## TL;DR

Endliche Automaten können verwendet werden, um die Eingaben zu validieren.
Sie sind sehr effizient und simpel zu implementieren, können aber schnell sehr komplex werden.
Oft kann man mit Regülaren Ausdrücken sehr einfach einen solchen Automaten formulieren, fast alle Sprachen haben diese schon eingebaut.
