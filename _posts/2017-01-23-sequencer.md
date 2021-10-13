---
layout: post
title: Sequencer
categories:
- StepSequencer
lang: de
---
Dieses Modul kümmert sich um das Anstoßen der anderen Module. Es speichert die Sequenz, die aktuelle Position innerhalb dieser sowie eine Liste an Methoden, die angestoßen werden.

```c#
public void tick(){
	position++;
	position %= stepAmount;
	foreach (Action action in methodsToCall){
		action();
	}
}
```

Innerhalb der `tick()`-Methode wird die Position um einen erhöht, und dann mit einer for-Schleife die Liste der Methoden ausgeführt.

```c#
public void resizePatterns(){
	//renders the pattern bigger
	for (int i = 0; i &lt; activePattern.Count; i++){
		bool[] newTrack = new bool[stepAmount];
		for (int j = 0; j &lt; stepAmount; j++){
			newTrack[j] = activePattern[i][j % activePattern[i].Length];
		}
		activePattern[i] = newTrack;
	}
}
```

Diese Funktion verlängert/verkürzt die Spuren auf die aktuelle Länge.
```c#
public bool loadPattern(string patternName){
	if (patternHolder.libraryPatternsList.ContainsKey(patternName)){
		patternHolder.loadPattern(patternName);
		activePattern = patternHolder.activePattern;
		resizePatterns();
		return true;
	} else {
		return false;
	}
}
```
Diese Methode prüft, ob es ein bestimmtes Pattern gibt, und läd es dann.
