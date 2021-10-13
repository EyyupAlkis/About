---
layout: post
title: Verschlüsselung
categories:
- Lernblog
- Algorithms
lang: de
---
Verschlüsselungen gibt es seit jeher. Mit ihnen ist es möglich, eine Nachricht so abzuwandeln, dass man sie nur mit dem zugehörigen Schlüssel lesen kann. Im Unterricht haben wir die Caesar- sowie die Vigenere-Verschlüsselung (eine Abwandlung von Caesar) besprochen.

Der Einfachheit halber betrachten wir Verschlüsselungen erstmal mit einer Zahl x als Beispiel. Dabei stellt ein großes X den entschlüsselten Wert dar, ein kleines x den verschlüsselten.

Möchte ich eine Zahl nun verschlüsseln, so kann ich zum Beispiel eine beliebige Zahl _n_ addieren.

{% latex %}X+n = x{% endlatex %}

Möchte nun jemand die Nachricht entschlüsseln, so muss er _n_ nur abziehen. Er erhält dann den Klartext. Es reicht also, einen Schlüssel zu vereinbaren, schon ist die Kommunikation verschlüsselt.

{% latex %}x-n = X{% endlatex %}

Dies kann man nun auf Buchstaben übertragen. Ich ordne jedem Buchstaben eine Zahl zu, im Falle Caesar 1 bis 26 in alphabetischer Reihenfolge. Nun wende ich auf jedes Zeichen des Klartexts die Verschlüsselung an.

Beispiel:

Schlüssel:  1  
Klartext:                                 A  u   t    o  
Indexe:                                   1  21 20 15

Verschlüsselt:                       2 22 21  16  
Geheimtext:                          B  V  U   P

In der Vigenere-Verschlüsselung wechselt man den Schlüssel nun. Zumeist wird die festgelegte Reihenfolge mithilfe eines Schlüsselwortes übergeben. Dabei entspricht der Index eines Buchstabes innerhalb des Alphabets dem genutzten Schlüssel: tifhpmab

Schlüssel als Wort:               t     o  m    t      o   m  t      o  
Schlüssel als Zahl:                20 15 13   20  15  13  20   15  
Klartext:                                  a    u   t     o     b   a    h     n  
Indexe:                                    1    21  20 15    2   1    8     14

Verschlüsselt:                        20  9   6    8    16  13  1     2  
Geheimtext:                           t      i    f     h    p    m  a     b

Möchte ich dies nun entschlüsseln, so schreibe ich wieder das Schlüsselwort über das Chiffre und Subtrahiere, statt zu addieren.

Ein Angriff auf das Caesarverfahren ist nicht allzu schwierig: Da es maximal 26 verschiedene Schlüssel gibt, kann ich einfach alle ausprobieren. Alternativ kann ich mithilfe einer Häufigkeitsanalyse die Häufigsten Buchstaben ermitteln, dann ist die Entschlüsselung auch nicht mehr schwer.  
Um Vigenere zu knacken, bedarf es deutlich größerem Aufwand: Ich muss zuerst einmal herausfinden, wie lang das Schlüsselwort ist. Die erreiche ich durch das aufspüren von Wiederholungen im Chiffre - hier trifft der gleiche Teil des Schlüssels auf das gleiche Wort. Nun kann ich wieder durch Häufigkeitsanalyse den Schlüssel ermitteln. Et Voila: Vigenere ist geknackt. Schwierig wird dies bei langen Schlüsseln, die Komplexität lässt sich durch folgenden Term beschreiben:  
Komplexität = 26^Länge des Schlüssels
