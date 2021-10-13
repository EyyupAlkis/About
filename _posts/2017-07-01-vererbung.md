---
layout: post
title: Vererbung
categories:
- Lernblog
- Learning Java
lang: de
---

Einer der großen Vorteile der Objekt-Orientierung ist die sog. Vererbung:  
Dabei "erbt" eine Klasse alle Methoden und Attribute ihrer Super-Klasse.

Ein typisches Beispiel dafür ist die Tierwelt:
```java
abstract class Animal{
  int legs;

  void eat(){
    System.out.println("Animal eats");
  }
}
```
```java
public class Cat extends Animal {
  String feltColour;
 
  Cat(){
    legs = 4;
  }
 
  @Override
  void eat(){
    System.out.println("Cat eats");
  }
}
```
```java
public class FelisFellais extends Cat {
  FelisFellais(){
    feltColour = "yellow";
  }
 
  void superEat(){
    super.eat();
  }
 
  @Override
  void eat(){
    System.out.println("FelisFellais eats");
  }
}
```

Auffällig ist ein neues Symbol: @Override  
Es stellt eine Art Kommentar dar, die dem Compiler mitteilt dass die folgende Methode die der Super-Klasse überschreibt.  
Dies ist ein wichtiges Merkmal der Objektorientierung: Eine Klasse kann die geerbten Methoden überschreiben, durch ihre eigene Implementierung ersetzen.

Dabei gibt es zwei nützliche Methoden, um die Redundanz (Dopplung) meines Codes zu minimieren:

### Generalisierung

Hier fasse ich die Übereinstimmungen zweier Klassen in einer weiteren, der sogenannten "Oberklasse" bzw. "Superklasse" zusammen. Beispiel: Ich habe eine Klasse Fahrrad und eine Klasse Auto, beide haben das Attribut Maximalgeschwindigkeit. Nun schreibe ich eine Oberklasse "Fahrzeug", die auch dieses Attribut besitzt. Ich schreibe nun die Klassen Fahrrad und Auto um und gebe an, dass sie von Fahrzeug erben. Die doppelten Attribute wie z.B. Maximalgeschwindigkeit kann ich nun aus den beiden Klassen löschen. Wichtig ist dabei: Die Subklasse muss auf das Super-Attribut zugreifen können, dafür muss der Modifier (z.B. public/private) das zulassen. In der folgenden Tabelle kann man ablesen, welches der richtige Modifier für seinen eigenen Fall ist:

|  Modifier     | Class | Package | Subclass | World |
| ------------- |-------|---------|----------|-------|
| `public`      | x     | x       | x        | x     |
| `protected`   | x     | x       | x        |       |
| `no modifier` | x     | x       |          |       |
| `private`     | x     |         |          |       |

### Spezialisierung

Spezialisierung bezeichnet das exakte Gegenteil der Generalisierung: Hier schreibe ich eine Unterklasse, die ein besonderes/spezielles Exemplar der Oberklasse ist. Am obigen Beispiel der Katze: Cat ist ein besonderes Animal, Felis Fellais ist eine besondere Cat. Wichtig dabei ist, dass die richtigen Modifier genutzt werden.

### Vererbung im UML-Diagramm

Im UML-Diagramm wird die Vererbung durch einen Pfeil zur Oberklasse gekennzeichnet.
