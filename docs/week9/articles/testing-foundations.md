{% include head.html %}
# Theoretische Grundlagen des Testens

*In diesem Artikel beschreiben wir kurz die wichtigen Theoretischen Grundlagen des Testens. Das Ziel ist, 
klar zu definieren was wir idealerweise erreichen wollen, aber auch zu realisiseren, dass wir diesese Ziel nie
ganz erreichen, sondern nur annähern können.*

## Einführung

In jeder Ingenieursdisziplin muss das Produkt nach der Herstellung verifiziert werden, um zu überprüfen, dass es die gewünschten Eigenschaften hat. 
Eine wichtige Methode für die Verifikation ist das Testen. Testen bedeutet, dass wir uns einige typische Szenarien anschauen, und schauen, ob sich 
das System in diesen Situation entsprechend unseren Erwartungen verhält. Wir haben am Anfang dieser Vorlesung gesehen, dass ein grosser Unterschied 
zwischen Software und anderen Produkten ist, dass wir in der Software keine Physikalischen Grundlagen haben, auf die wir uns verlassen können. 
Dies macht nicht nur die Entwicklung schwieriger, sondern macht uns auch beim Testen das Leben schwer. Um das zu sehen, vergleichen wir 
einen typischen Testfall für eine Brücke, mit dem einer Software:. 
Ein typisches Szenario beim Testen einer Brücke könnte sein, dass wir testen wollen, ob die Brücke die spezifizierte Last von $$1000t$$ tragen kann.
Dazu belasten wir die Brücke mit dem entsprechenden Gewicht. Wenn die Brücke unter der Last nicht zusammenbricht haben wir einiges gelernt. Wir wissen 
nicht nur, dass die Brücke $$1000t$$ Belastung tragen kann, sondern auch jede Belastung die kleiner als $1000t$ ist. Dies können wir unmittelbar aus
den Physikalischen Gegebenheiten schliessen, da alle relvanten Physikalischen Gesetzte eine entsprechende Kontinuität aufweisen.

Im Gegensatz dazu können wir bei der Software nicht von Kontinuität ausgehen. Dazu schauen wir uns ein Codebeispiel an:
```java
    int binarySearch(T key, List<Double> list) {
        int bottom = 0; int top = list.length(); int middle = 0;
        while (bottom < top) {
            if ((bottom + top) % 2 == 0) {
                middle = (bottom + top) / 2;
            } else {
                middle = ??? /* fehlerhafte Berechnung */
            }
            if (key &le; list.get(middle)) {
                top = middle;
            } else {
                bottom = middle + 1;
            }
        }
                    
    }
```
In diesem Beispiel tritt die Fehlerhafte Berechnung nur dann auf, wenn wir eine Liste sortieren, bei der die Anzahl Elemente genau einer Zweierpotenz entspricht. 
Wenn wir also etablieren, dass das Korrekte Element  für eine Liste mit 1025 Elementen gefunden wird, können wir daraus nicht schliessen, dass dies auch für eine Liste mit 1024 Elementen gilt.


### Weitere praktische Schwierigkeiten

Zu diesen Theoretischen Herausforderungen vom Testen, kommen noch eine ganze Menge praktischer Herausforderungen. 
Zum Beispiel können Systeme nicht-deterministisch sein. Das heisst, wenn wir zwei mal mit denselben Inputdaten testen, wird nicht dasselbe herauskommen. 
Dies ist zum Beispiel immer dann ein Problem, wenn wir mit Zufallszahlen arbeiten, oder wenn unsere Programme Nebenläufig sind. Ein weiteres Problem ist, 
dass Software immer als Teil eines grösseren Systems läuft (zum Beispiel auf einem Betriebssystem), welches meist selbst wieder Fehler aufweisen kann.

Wir werden im Moment aber solche Probleme ausblenden, und uns nur um die Herausforderungen, die in einem idealisierten Setting auftauchen, kümmern.

## Programme, Fehler und Korrektheit

Als erstes definieren wir formal, was ein Programm ist. Wir können ein Programm immer als mathematische Funktion 

$$
P : D \to R
$$
anschauen, wobei $$D$$ die Menge der möglichen Inputdaten und $$R$$ die Menge der Möglichen Outputs ist. 

*Anmerkung: Wenn ein Programm Seiteneffekte hat, also zum Beispiel etwas auf die Festplatte speichert, eine Datei löscht, etc., wäre diese Definition direkt so nicht
Anwendbar. In diesem Fall können wir aber alle Seiteneffekte als Teil vom Output beschreiben, und diese erste nach dem Beenden des Programms ausführen. Damit ist unsere
Betrachtung des Programms als Mathematische Funktion wieder anwendbar.*

Um zu definieren, wann ein Programm korrekt ist, definieren wir uns die Relation 
$$OR \subseteq D \times R$$, 
also die Menge aller möglichen Input/Output Paare. 
* Ein Programm $$P$$ ist korrekt für Input $$d$$, wenn gilt, dass $P(d) \in OR$
* Ein Programm $$P$$ ist korrekt, wenn für alle $$d \in D$$, das Programm $$P(d)$$ korrekt ist für input $d$.
In Worten formuliert ist ein Programm also korrekt, wenn es für alle möglichen Eingaben die richtige Ausgabe produziert.
Umgekehrt ist ein fehlerhaft wenn es nicht korrekt ist. Das heisst, 
wir können ein Paar $$(d, P(d))$$ finden, so dass 

$$
(d, P(d)) \notin OR
$$

Da die Menge aller möglichen Eingaben/Ausgaben Paare natürlich sehr gross ist, kann es sein, dass ein Programm Fehlerhaft ist, ohne dass wir den Fehler je sehen. 

## Testfälle und Testmenge

Mit diesen Definitionen können wir uns nun auch definieren, was testen genau ist. 
Dazu definieren den Begriff Testfall als ein Element $$t \in D$$. Wir sagen, dass ein Test erfolgreich ist, wenn $$P(t) korrekt ist$$. 
Entsprechend ist eine Testmenge als eine Menge von Testfällen, also eine Untermenge $$T \subset D$$ definiert und wir sagen, dass eine 
Testmenge erfolgreich ist, wenn $$P(t)$$ korrekt ist für alle $$t \in T$$.

Testen bedeutet, dass wir das Programm für eine Testmenge von Inputs auswerten, und schauen ob das Resultat korrekt ist.

## Ideale Testmenge
Da die Menge aller möglichen Testfälle viel zu gross ist, können wir für realistische Programme nur eine winzige Untermenge aller möglichen Testfälle durchführen. 
Wir möchten diese aber so auswählen, dass diese Testmenge möglichst alle Fehler findet. Dies bringt uns zum Begriff der idealen Testmenge. 
Eine Testmenge $$T$$ ist ideal, wenn gilt dass:
* $$P(t)$$ ist fehlerhaft dann $$t \in T$$.
Das heisst, eine ideale Testmenge zeigt immer einen Fehler auf, wenn ein Fehler existiert. Wenn kein Fehler existiert, das Programm also korrekt ist, dann 
ist jede Testmenge eine ideale Testmenge. 
Wenn eine Menge $$T$$ eine ideale Testmenge ist, und $$P(t)$$ korrekt ist f&uuml;r all $$t \in T$$, dann ist $P$ korrekt.
Falls wir also eine ideale Testmenge hätten, dann könnten wir die korrektheit eines Programms durch Testen beweisen. 

## Unentscheidbarkeit 

Leider kann man zeigen, dass es im allgemeinen nicht möglich ist, eine ideale Testmenge zu finden. Wir können also keinen Algorithmus schreiben, 
welcher uns die ideale Testmenge liefert. Nehmen wir an, dass wir das Programm mit Hilfe von erster Ordnung logik formal spezifiziert haben. Wir haben also 
eine Funktion $$W(d,u)$$, die uns für jeden input $$d$$ und Output $$u$$ Wahr oder Falsch zurückgibt, je nachdem ob das Verhalten des Programms gemäss Spezifikation korrekt ist. 
Ein Programm wäre dann also Korrekt, wenn gilt, dass
$$
\texttt{für alle } d \texttt{in} D, u \texttt{in} R, (u = P(d)) \texttt{ impliziert} W(d, u)
$$
Ein klassisches Resultat aus der mathematischen Logik (und theoretischen Informatik) besagt aber, dass dies ein Unentscheidbares Problem ist. Somit können wir durch 
Testen weder die Korrektheit eines Programms beweisen, noch eine ideale Testmenge finden. 



## Der Pragmatische Testansatz: Äquivalenzklassen

Trotz diesem enttäuschenden Resultat hilft uns diese Einsicht doch weiter. Wir wissen nun, dass wir eigentlich nur eine ideale Testmenge finden müssten um die 
Korrektheit des Programms zu beweisen. Wir können diese zwar nicht berechnen, können aber zumindest versuchen diese anzunähern, indem wir eine möglichst kleine Testmenge findet, 
die mit hoher Wahrscheinlichkeit die Fehler findet. 
Schauen wir uns dazu ein Beispiel an:
```java
    int max(int x, int y) {
        if (x > y) { 
            return x;
        } else {
            return y;
        }
    }
```
Zwei mögliche Testmengen wären nun 

$$\{x = 3, y =2; x=2, y=3 \}$$ 

und
$$\{x=3, y=2; x=4,y = 3; x=5, y = 1\}$$.

Die erste Testmenge ist hier eindeutig zu bevorzugen, da diese nicht nur den ersten Pfad der Bedingung testet, sondern auch den zweiten. Wir sehen hier, dass es also nicht die Anzahl der Testfälle ausschlaggebend für die Güte von der Testmenge ist. 
Stattdessen versuchen wir usere ideale Testmenge approximieren ist, indem wir Testfälle definieren, die repräsentativ für eine ganze Klasse von Testfällen stehen. In diesem Beispiel repräsentiert $$x=3,y=2$$ alle Testfälle, bei der die Bedingung $$\texttt{true}$$ ist, 
und $$x=2,y=3$ diejenige Klasse, für die die erste Bedingung $$\texttt{falsch}$$ ist. Jeder weitere Testfall würde hier nicht mehr helfen weitere Fehler zu entdecken.

Das allgemeinere Konzept, das hier zugrunde liegt ist das definieren von Äquivalenzklassen von Tests. Im oberen Beispiel haben wir die Äquivalenzklassen gemäss der Struktur vom Code definiert (also welcher Teil der if Bedinung erfüllt war). 
Wir werden nächste Woche sehen, dass es ganz verschiedene Arten gibt die Äquivalenzklassen zu formulieren. Auf abstrakter Ebene, können wir aber bereits unser Ziel formulieren. Wir möchten 
unsere Testfälle in Äquivalenzklassen $$D_1, D_2, \ldots, D_n$$ einteilen, so dass
* Elemente von $D_i$ dasselbes Verhalten haben
* wir eine möglichst *vollst&auml;ndige Testabdeckung erreichen, also $$\cup_i D_i = D$$
* die Testfälle sind möglichst spezifisch für eine Gruppe sind, also $D_i \cap D_j = \emptyset$. Falls $D_i \cap D_j \neq \emptyset$, sollte der Testfall in der Schnittmenge der Klassen liegen, also sowohl die kriterien von Äquivalenzklasse $$i$$ also auch der Klasse $$j$$ testen.

Dieser Sachverhalt wird in untenstehender Abbildung illustriert.
<img src="../../slides/images/equivalenceclasses.png" style="width:30%" class="plain"/>


           