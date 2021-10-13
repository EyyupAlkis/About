---
layout: post
title: "Kellerautomaten"
categories:
  - "Lernblog"
  - "Theoretical Computer Science"
lang: de-DE
---

Im vorletzten Post wurden [Endliche Automaten]({% link _posts/2018-11-24-Endliche-Automaten.md %}) erläutert.
Dieser Automatentyp ist zwar sehr einfach, aber auch beschränkt in seiner Anwendung.
In diesem Post wird der *Kellerautomat* erläutert - ein Automat, der einen Speicher besitzt und so zum Beispiel auch Palindrome erkennen kann.

<img src="/assets/kellerautomat/keller.svg" style="max-width: 40%" alt="Keller">

<!--more-->

## Der Keller

Gegenüber einem Endlichen Automaten unterscheidet sich ein Keller-Automat durch den *Keller*.
Der *Keller* ist ein Stack, mithilfe dessen sich der Automat Dinge merken kann.
Wichtig: Man kann bei einem Stack neue Werte nur oben anfügen sowie den obersten Wert auslesen.
Liest man den obersten Wert aus, wird dieser auch sofort entfernt[^stack-post].

[^stack-post]: Wie man einen Stack implementiert, kann [in meinem Post über Stacks]({{ site.baseurl }}{% link _posts/2017-12-13-Stack.md %}) nachgelesen werden.

Die Definition eines Kellerautomat wächst im Vergleich zum Endlichen Automaten um zwei Parameter:

Das Keller-Alphabet {% latex %}\Gamma{% endlatex %}, welches alle für den Keller zulässigen Symbole enthält, sowie das Initial-Symbol des Kellers, bezeichnet als {% latex %}Z{% endlatex %}.

{% latex centred %}
A = (Q, \Sigma, \Gamma, \delta, q_0, Z, F)
{% endlatex %}


{% latex %}\delta{% endlatex %}, die Übergangsfunktion, verändert sich leicht, um nun zusätzlich die Veränderung des Kellers abzubilden.

### Ein Beispiel

Ein einfacher Kellerautomat wäre dieser, der die Sprache {% latex %}L=\{ w \in \Sigma^* | w=a^nb^n, n > 0 \}{% endlatex %} ({% latex %}n{% endlatex %} mal "a", dann {% latex %}n{% endlatex %} mal "b") erkennt.

{% graphviz %}
digraph anbn {
  rankdir=LR;
  size="8,5";

  node [shape = doublecircle];q2;
  node [shape = point]; qi;

  node [shape = circle];
  qi -> q0;
  q0 -> q0 [ label = "a,#;#A" ];
  q0 -> q0 [ label = "a,A;AA" ];
  q0 -> q1 [ label = "b,A;&epsilon;" ];
  q1 -> q1 [ label = "b,A;&epsilon;" ];
  q1 -> q2 [ label = "&epsilon;,#;#" ];
}
{% endgraphviz %}

Wie liest man so einen Übergangsgraphen?  
Der Pfeil `a,#;#A` von {% latex %}q_0{% endlatex %} nach {% latex %}q_0{% endlatex %} bedeutet:
Wenn du in {% latex %}q_0{% endlatex %} bist, ein "a" eingelesen hast und der oberste Wert des Kellers ein "#"[^bottom] ist, dann gehe nach {% latex %}q_0{% endlatex %} und lege ein "#" sowie ein "A" auf den Keller drauf.

[^bottom]: "#" ist per Konvention immer das unterste Element des Kellers.

Der obige Automat funktioniert so:

Zu Beginn werden alle "a"s des Wortes eingelesen, für jedes eingelesene "a" wird ein "A" auf den Keller gelegt.  
Die Anzahl der "A" im Keller repräsentiert also die Anzahl der "a", mit denen die Eingabe beginnt.

Sobald das erste "b" kommt, wird zu {% latex %}q_1{% endlatex %} gewechselt.
Nun wird für jedes folgende "b" ein "A" aus dem Keller genommen.

Am Ende des Wortes (wenn das leere Wort &epsilon; eingelesen wird) steht also im Keller ein "A" für jedes "a" ohne entsprechendes "b".
Ist der Keller leer ("#" ist das oberste Keller-Symbol), ist die Anzahl der "a" gleich der Anzahl der "b" und das Wort wird angenommen.

## Was kann ein Kellerautomat?

Ein Kellerautomat kann sich Dinge über seine Eingabe merken, dadurch kann er deutlich mehr Sprachen abbilden als ein Endlicher Automat.

So kann ein Kellerautomat zum Beispiel auch Palindrome erkennen, deren Wortmitte markiert ist:

{% latex centred %}
L = \{ w \in \Sigma^* | w = m0m_{reverse}, m \in (\Sigma \setminus{0})^* \}
{% endlatex %}

{% graphviz %}
digraph anbn {
  rankdir=LR;
  size="8,5";

  node [shape = doublecircle];q2;
  node [shape = point]; qi;

  node [shape = circle];
  qi -> q0;
  q0 -> q0 [ label = "a,#;#A" ];
  q0 -> q0 [ label = "a,A;AA" ];
  q0 -> q0 [ label = "a,B;BA" ];
  q0 -> q0 [ label = "b,A;AB" ];
  q0 -> q0 [ label = "b,B;BB" ];
  q0 -> q1 [ label = "0,A;A" ];
  q0 -> q1 [ label = "0,B;B" ];
  q1 -> q1 [ label = "b,B;&epsilon;" ];
  q1 -> q1 [ label = "a,A;&epsilon;" ];
  q1 -> q2 [ label = "&epsilon;,#;#" ];
}
{% endgraphviz %}

Der gezeigte Automat ist ein *deterministischer* Kellerautomat.

Mit einem *nichtdeterministischen* Kellerautomaten kann man ein Palindrom auch ohne eine Markierung der Wortmitte erkennen:

{% latex centred %}
L = \{ w \in \Sigma^* | w = mm_{reverse}, m \in \Sigma^* \}
{% endlatex %}

{% graphviz %}
digraph anbn {
  rankdir=LR;
  size="8,5";

  node [shape = doublecircle];q2;
  node [shape = point]; qi;

  node [shape = circle];
  qi -> q0;
  q0 -> q0 [ label = "a,#;#A" ];
  q0 -> q0 [ label = "a,A;AA" ];
  q0 -> q0 [ label = "a,B;BA" ];
  q0 -> q0 [ label = "b,A;AB" ];
  q0 -> q0 [ label = "b,B;BB" ];
  q0 -> q1 [ label = "a,A;&epsilon;" ];
  q0 -> q1 [ label = "b,B;&epsilon;" ];
  q1 -> q1 [ label = "b,B;&epsilon;" ];
  q1 -> q1 [ label = "a,A;&epsilon;" ];
  q1 -> q2 [ label = "&epsilon;,#;#" ];
}
{% endgraphviz %}

Daraus wird erkenntlich, dass es, anders als beim Endlichen Automaten, keine einfache Übersetzung zwischen *deterministischen* und *nichtdeterministischen* Kellerautomaten gibt - der *nichtdeterministische* kann mehr Sprachen erkennen.

## Was kann ein Kellerautomat *nicht*?

Der Schwachpunkt des Kellerautomaten ist der Keller:
Da der Keller nicht beliebig beschrieben werden kann, sind bestimmte Sprachen nicht durch ihn erkennbar.

Ein Beispiel: {% latex %}L = \{ w \in \Sigma^* | a^nb^nc^n \}{% endlatex %}

Diese Sprache kann von einem Kellerautomaten nicht erkannt werden, da er zum validieren der "b"s bereits seinen Keller leeren muss.
Um die Menge der "c"s zu validieren fehlen ihm die nötigen Keller-Elemente.

Auch die Sprache {% latex %}L = \{ w \in \Sigma^* | mm, m \in \Sigma^* \}{% endlatex %} kann vom Kellerautomaten nicht erkannt werden, da der Keller dafür von unten nach oben ausgelesen werden müsste.

Solche Sprachen können von einer Turing-Maschine erkannt werden, die in einem zukünftigen Post erklärt wird.

## TL;DR

Der Kellerautomat erweitert den Endlichen Automaten um einen Speicher.
Dieser Speicher ist ein Stack, in dem Elemente des Keller-Alphabets {% latex %}\Gamma{% endlatex %} gespeichert werden.

Ein Kellerautomat, der die Sprache {% latex %}a^nb^n{% endlatex %} erkennt, sieht so aus:

{% graphviz %}
digraph anbn {
  rankdir=LR;
  size="8,5";

  node [shape = doublecircle];q2;
  node [shape = point]; qi;

  node [shape = circle];
  qi -> q0;
  q0 -> q0 [ label = "a,#;#A" ];
  q0 -> q0 [ label = "a,A;AA" ];
  q0 -> q1 [ label = "b,A;&epsilon;" ];
  q1 -> q1 [ label = "b,A;&epsilon;" ];
  q1 -> q2 [ label = "&epsilon;,#;#" ];
}
{% endgraphviz %}

Die Menge der Sprachen, die ein *nichtdeterministischer* Kellerautomat erkennt, ist eine Übermenge der Sprachen, die ein *deterministischer* Kellerautomat erkennt.

Auch ein Kellerautomat erkennt nicht alle Sprachen, er ist durch den Stack beschränkt.
Eine Turingmaschine kann zum Beispiel die Sprache {% latex %}a^nb^nc^n{% endlatex %} erkennnen.