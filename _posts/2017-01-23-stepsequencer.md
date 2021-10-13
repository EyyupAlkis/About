---
layout: post
title: Einführung
categories:
- StepSequencer
lang: de
---

In den Ferien habe ich mich ein wenig mit der iOS-Entwicklung auseinander gesetzt. Dafür habe ich zuerst das Apple-Developer Swift-Tutorial durchgearbeitet, aber relativ schnell festgestellt, dass ich nicht noch eine neue Sprache lernen möchte. Außerdem wollte ich mich ein bisschen mit Cross-Plattform-Development bekannt machen.

Nach einigem Suchen nach Alternativen zu _XCode_ (Apples IDE) bin ich auf _Visual Studio for Mac_ gestoßen, eine kostenlose Preview zu Microsofts neuer Entwicklungsumgebung für Mac.

Diese entstammt der Feder von Xamarin Studios, einem StartUp-Unternehmen die Microsoft Anfang letzten Jahres übernommen hat. Visual Studio for Mac ist offensichtlich nicht viel anders als Xamarin, selbst das UI ist weitestgehend gleich geblieben.

Anders als Visual Studio 2017 für Windows ist die Mac-Version stark auf Mobile Development ausgelegt, ganz nach Microsofts Motto "Mobile First, Cloud First".

Das schöne an Visual Studio: Ich muss einen Großteil meines Codes nur einmal schreiben, dieser kann dann sowohl in der Android- als auch in der iOS-Version genutzt werden.

Standard-Sprache ist C#, welche sehr ähnlich zu Java ist. Der größte Vorteil mMn: ASync-Klassen brauch man nicht so oft, viele Dinge lassen sich über .delta Funktionen oder ähnliches lösen.

### Was möchte ich entwickeln?

Da ich viel Spaß an Musik habe, setze ich mir zum Ziel, einen funktionierenden Step-Sequencer zu entwickeln, zuerst nur für iOS, später auch für Android.

Ein Step-Sequencer kann man sich folgendermaßen vorstellen: In einem einstellbaren Intervall wird eine einstellbare Anzahl an Schritten durchlaufen. Nun kann ich für jeden Schritt einstellen, welche MIDI-Signale gesendet werden. MIDI ist ein Protokoll, welches genutzt wird um Musikinstrumente miteinander zu verbinden.

Hauptanwendung eines Step-Sequencers sind sog. Drum-Sequencer, da man dort keine Tonhöhen angeben muss. Neben einem Sequencer beinhalten diese Geräte immer noch einen Klangerzeuger, der vom Sequencer angesteuert wird. Dieser kann sowohl digital (auf Samples basiert / digital Erzeugter Klang durch DSP) oder analog (vollständig analoge Signalkette) ausfallen.

Die berühmteste Drum-Machine ist der Roland TR-909\. Sie wurde nur von 1984-85 gebaut, beherrscht allerdings noch heute viele Musik-Richtungen (Techno/House ohne 909 wäre undenkbar).

![TR909](https://upload.wikimedia.org/wikipedia/commons/c/cc/Roland_TR-909.jpg "TR-909")

Die Besonderheit zur damaligen Zeit war, dass Bass-Drum und Snare vollständig analog erzeugt wurden, die Percussion-Sounds wie Hi-Hat oder Crash allerdings auf Samples basierten. Dies macht den charakteristischen Klang des Geräts aus, der heute in unzähligen Klonen oder Software-Versionen zu finden ist.

Langzeit-Ziel ist es, die Wiedergabe meines Synthesizers ohne Samples zu ermöglichen. Dafür werde ich mich mit Sound-Synthese beschäftigen und ein Plugin auf Basis von Apple's Audio-Unit- sowie Steinberg's VST-Standard schreiben.
