---
layout: post
title: ObjektOrientierung
categories:
- Lernblog
- Learning Java
lang: de
---
Ein besonders wichtiges Modell der Informatik sind sogenannte Objekte - Modellierungen realer Gegenstände durch die primitiven Datentypen. Diese ermöglichen es, sehr komplexe Modelle mit vielen Variablen deutlich übersichtlicher und flexibler zu gestalten.  
Ein Objekt wird durch eine Klasse deklariert und folgendermaßen implementiert:

```java
class Xyz{
  private int a;
  private double b;
  private String c;

  public Xyz(int a, double b, String c){
    setA(a);
    setB(b);
    if (!c.equals("")) this.c = c;
  }

  public int getA(){ return this.a; }
  public double getB(){ return this.b; }
  public String getC(){ return this.c; }

  public void setA(int a){ if(a > 0) this.a = a; }
  public void setB(double b){ if(b > 0) this.b = b; }
}
```

Dabei werden oben die Attribute mit Standardwerten deklariert, es folgt der _Konstruktor_ und dann die _get-/set-Methoden_.

_Attribute_ speichern die Eigenschaften des Objekts. Sie sind ganz normale Variablen! Allerdings definiert man sie als _private_, damit man nur über die _get-/set- Methoden_ darauf zugreifen kann.

Der _Konstruktor_ erzeugt das Objekt, ihm müssen also alle wichtigen Werte übergeben werden. Er ist die einzige Funktion ohne Rückgabewert!

Die _get-/set-Methoden_ sind dafür da, dass man mit dem Objekt interagieren kann. Sie sehen immer gleich aus und verschaffen der Umgebung Zugriff auf die Attribute.

Um ein _Objekt_ zu _erzeugen_, deklariere ich es wie eine normale Variable und initialisiere es dann, indem ich seinen _Konstruktor_ aufrufe:

```java
Xyz meinObjekt = new Xyz(20, 17.4, "April");
```

Wichtig ist:  
Ein Objekt kann ich genau wie eine normale Variable an Funktionen übergeben. Während ich bei primitiven Datentypen allerdings den Wert dieser übergebe, arbeitet man bei Objekten mit sog. Pointern - die Variable "zeigt" auf das Objekt. Hier muss ich darauf achten, dass meine Funktion die Attribute des Objekts nicht ungewollt verändert - schließlich arbeitet sie nicht mit einer Kopie dessen.  
Möchte ich ein Objekt ausdrücklich kopieren, so kann die .clone()-Funktion benutzen, die jedes Objekt implementiert.
