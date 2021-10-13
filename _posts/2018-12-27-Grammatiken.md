---
layout: post
title: "Grammatiken"
categories:
  - "Lernblog"
  - "Theoretical Computer Science"
lang: de-DE
---

Eng in Verbindung mit den in den letzten beiden Posts behandelten Automaten stehen sogenannte *Grammatiken*.  
Während ein Automat beurteilt, ob ein Wort zur dargestellten Sprache gehört, erzeugt eine *Grammatik* Wörter einer Sprache.

<img src="/assets/grammatik/duden.svg" style="max-width: 40%" alt="Grammatik">

<!--more-->

## Funktionsweise

Die Anwendung einer Grammatik beginnt immer mit einem Startsymbol, welches dann nach den Ableitungsregeln der speziellen Grammatik abgeleitet wird.

Ein einfaches Beispiel:

Wir beginnen mit dem Startsymbol {% latex %}S{% endlatex %}.

Unsere Ableitungsregeln lauten:

{% latex centred %}
S \rightarrow aA
{% endlatex %}
{% latex centred %}
A \rightarrow bB
{% endlatex %}
{% latex centred %}
B \rightarrow c
{% endlatex %}

{% latex %}S{% endlatex %} wird zu {% latex %}aA{% endlatex %} abgeleitet, {% latex %}aA{% endlatex %} zu {% latex %}abB{% endlatex %}, {% latex %}abB{% endlatex %} zu {% latex %}abc{% endlatex %}.
Die obige Grammatik erzeugt nur ein einziges Wort {% latex %}abc{% endlatex %}.

Man geht also immer von links nach rechts durch das Wort und ersetzt mithilfe der Ableitungsregeln den ersten vorhandenen Großbuchstaben.
Dies wird solange wiederholt, bis das Wort keine Großbuchstaben mehr enthält.

Gibt es mehrere anwendbare Ableitungsregeln, so kann man sich eine davon aussuchen, wie zum Beispiel bei der Grammatik zu {% latex %}a^+{% endlatex %}:

{% latex centred %}
S \rightarrow aA
{% endlatex %}
{% latex centred %}
A \rightarrow aA | a
{% endlatex %}

## Formale Notation

Eine Grammatik {% latex %}G{% endlatex %} besteht aus vier Elementen:

{% latex centred %}
G = (N, T, R, S)
{% endlatex %}

{% latex %}N{% endlatex %} beschreibt die *Nichtterminalsymbole* - Symbole, die nur Platzhalter sind und später ersetzt werden.
Per Konvention werden dafür Großbuchstaben verwendet, im obigen Beispiel sind es {% latex %}S{% endlatex %}, {% latex %}A{% endlatex %} und {% latex %}B{% endlatex %}.

{% latex %}T{% endlatex %} beschreibt die *Terminalsymbole* - Symbole, aus denen das fertige Wort besteht.
Per Konvention werden dafür Kleinbuchstaben verwendet, im obigen Beispiel sind es {% latex %}a{% endlatex %}, {% latex %}b{% endlatex %} und {% latex %}c{% endlatex %}.

{% latex %}R{% endlatex %} ist eine Menge aus Ableitungsregeln.
Diese leiten von einer Kombination aus *Terminal-* und *Nichtterminalsymbolen* zu einer anderen, längeren Kombination weiter.

{% latex %}S{% endlatex %} ist das Startsymbol - ein Element aus {% latex %}N{% endlatex %}, mit welchem die Ableitung begonnen wird.

## Arten von Grammatiken

Es gibt verschiedene Arten von Grammatiken, die verschiedene Anforderungen an die {% latex %}R{% endlatex %} stellen.
Jede Art der Grammatik hat eine zugehörige Art von Automaten, die die selben Sprachtypen abbilden können.

### Reguläre Grammatiken

Ableitungsregeln einer regulären Grammatik zeichnen sich dadurch aus, dass sie von einem Nichtterminalsymbol zu einer Kombination aus entweder einem Terminalsymbol oder einem Terminalsymbol und einem Nichtterminalsymbol ableiten.

Man nennt eine Grammatik rechtsregulär, wenn das Nichtterminalsymbol immer rechts vom Terminalsymbol steht.

{% latex centred %}
S \rightarrow aA
{% endlatex %}
{% latex centred %}
A \rightarrow a | aA
{% endlatex %}

Dann wächst ein Wort beim Ableiten von links nach rechts:

{% latex centred %}
S \rightarrow aA \rightarrow aaA \rightarrow aaa
{% endlatex %}

Man nennt eine Grammatik linksregulär, wenn das Nichtterminalsymbol immer links vom Terminalsymbol steht.
Dann wächst ein Wort beim Ableiten von rechts nach links.

Die Klasse der Sprachen, die eine Reguläre Grammatik erzeugen kann, ist identisch mit der, die ein endlicher Automat erkennen kann.
In der Chomsky-Hierarchie sind dies die regulären Sprachen (Typ 3).

Aufgrund der selben Klasse kann man aus Rechtsregulären Grammatiken einfach den zugehörigen Endlichen Automaten erzeugen:

{% latex centred %}
S \rightarrow aA
{% endlatex %}
{% latex centred %}
A \rightarrow a | aA
{% endlatex %}

Die Nichtterminalsymbole werden zu den Zuständen des Automaten, die rechten Seite der Ableitungsregeln zu den Übergängen.

{% graphviz %}
digraph finite_state_machine {
  rankdir=LR;
  size="8,5"

  node [shape = doublecircle]; A;
  node [shape = point]; qi;

  node [shape = circle];
  qi -> S;
  S -> A [ label = "a" ];
  A -> A [ label = "a" ];
}
{% endgraphviz %}


- Aus der Ableitungsregel {% latex %}S \rightarrow aA{% endlatex %} wird der Übergang von {% latex %}S{% endlatex %} nach {% latex %}A{% endlatex %} mit der Eingabe {% latex %}a{% endlatex %}.
- Aus der Ableitungsregel {% latex %}A \rightarrow aA{% endlatex %} wird der Übergang von {% latex %}A{% endlatex %} nach {% latex %}A{% endlatex %} mit der Eingabe {% latex %}a{% endlatex %}.
- Aus der Ableitungsregel {% latex %}A \rightarrow a{% endlatex %} wird entnommen, dass {% latex %}A{% endlatex %} ein Endzustand ist.

Die Umwandlung vom DEA zur Regulären Grammatik funktioniert ähnlich.

#### Was kann eine Reguläre Grammatik?

Eine Reguläre Grammatik kann die selben Sprachen wie ein Endlicher Automat abbilden.
Sie scheitert bei Sprachen, deren Erkennung einen Zwischenspeicher benötigt - zum Beispiel {% latex %}a^nb^n{% endlatex %}.

### Kontextfreie Grammatiken

Kontextfreie Grammatiken behalten die Einschränkung, dass auf der linken Seite der Ableitungsregeln ein Nichtterminalsymbol stehen muss.
Auf der Rechten Seite kann nun bei den kontextfreien Grammatiken allerdings eine beliebige Kombination aus Terminal- und Nichtterminalsymbolen stehen.

So kann man mit kontextfreien Grammatiken die Sprache der Palindrome sehr einfach darstellen:

{% latex centred %}
S \rightarrow aSa | bSb | a | b | aa | bb
{% endlatex %}

Auch {% latex %}a^nb^n{% endlatex %} ist einfach als kontextfreie Grammatik zu beschreiben:

{% latex centred %}
S \rightarrow ab | aSb
{% endlatex %}

Die Klasse der Sprachen, die durch kontextfreie Grammatiken erzeugt wird, kann durch Nichtdeterministische Kellerautomaten erkannt werden.
In der Chomsky-Hierarchie sind dies die kontextfreien Sprachen (Typ 2).

Die Übersetzung zwischen Automat und Grammatik ist bei kontextfreien Sprachen deutlich komplizierter als bei Sprachen des Typ 3.

### Kontextsensitive Grammatiken

Kontextsensitive Grammatiken lösen auch die Einschränkung der Linken Seite der Ableitungsregeln auf.
Hier kann nun eine beliebige Kombination aus mindestens einem Nichtterminalsymbol und beliebig vielen Terminalsymbolen verwendet werden.

Diese Grammatik erzeugt die Sprache {% latex %}a^nb^nc^n{% endlatex %}:

{% latex centred %}
S \rightarrow abc | aAbc
{% endlatex %}
{% latex centred %}
Ab \rightarrow bA
{% endlatex %}
{% latex centred %}
Ac \rightarrow Bbcc
{% endlatex %}
{% latex centred %}
bB \rightarrow Bb
{% endlatex %}
{% latex centred %}
aB \rightarrow aa | aaA
{% endlatex %}

Die Ableitungsregeln werden also, im Gegensatz zu kontextfreien Grammatiken, abhängig *(sensitiv)* von ihrem Kontext, den Buchstaben um das Nichtterminalsymbol herum, ausgewählt.

Kontextsensitive Grammatiken bilden Kontextsensitive Sprachen (Typ 1) ab, der zugehörige Automatentyp ist die Turing-Maschine.

#### Turing-Maschine

Die Turing-Maschine ist ein Automat, der ein endloses Speicherband besitzt.
Auf diesem Speicherband befindet sich zu Beginn die Eingabe in den Automaten, am Ende sollte dort das Ergebnis des Automaten stehen.
So kann eine Turing-Maschine nicht nur eine Eingabe Validieren, sondern auch ein Ergebnis zurückgeben.

Mit einem Lese-Schreib-Kopf kann das aktuelle Element des Speicherbands ausgelesen und geschrieben werden, bei jedem Übergang kann man ihn ein Feld nach links oder rechts verschieben.

Christian Spannagel erklärt die Turingmaschine sehr gut in diesem Video:

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/fd9JUjnlpoY" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## TL;DR

Im Gegensatz zu Automaten, der Wörter einer Sprachen erkennt, produzieren Grammatiken Wörter einer Sprache.
Sie tuen dies durch mehrmaliges Ableiten nach gegebenen Ableitungsregeln.
Je nach dem, welche Eigenschaften diese Ableitungsregeln besitzen, spricht man von verschiedenen Arten der Formalen Grammatik:

| Typ in der Chomsky-Hierarchie | zugehörige Grammatik | zugehöriger Automat | Beispielsprache | 
| - | - | - | - |
| Typ 3 | Reguläre Grammatik | Endlicher Automat | {% latex %}a^n{% endlatex %} |
| Typ 2 | Kontextfreie Grammatik | Nichtdeterministischer Kellerautomat | {% latex %}a^nb^n{% endlatex %} |
| Typ 1 | Kontextsensitive Grammatik | Turingmaschine | {% latex %}a^nb^nc^n{% endlatex %}
