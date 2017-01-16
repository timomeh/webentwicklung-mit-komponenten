# Was sind Komponenten?

Eine Komponente (engl. _component_) bezeichnet in der Webentwicklung einen Teil eines User Interfaces. Dementsprechend bilden viele Komponenten gemeinsam ein User Interface. Bestenfalls besteht das User Interface zu 100% aus Komponenten.  
Eine Komponente besteht aus HTML, meistens CSS und oft auch JavaScript. Auch Bilder, Icons u.ä. gehören zu einer Komponente.

Dadurch wird die **Komplexität** des User Interfaces auf die Komponenten aufgeteilt, wodurch viele Komponenten entstehen, die jeweils weniger komplex und für sich selbst verantwortlich sind.

Twitter nennt in ihrer Komponenten-Architektur _"SUIT CSS"_ unter anderem<sup>1</sup> folgende Merkmale einer Komponente:

1. **Modularität** (Modularity)  
   Eine Komponente kümmert sich nur um einen einzigen Teil des User Interfaces. Sie sind kontextfrei, d.h. sie beachten nichts außerhalb von sich selbst.

2. **Composable and configurable** (Komposition und Konfiguration)  
   Komponenten können in verschiedenen Kombinationen verbunden bzw. verschachtelt werden, je nach Notwendigkeit. Über Interfaces können sie konfiguriert werden.

3. **Loose coupling** (Lose Kopplung)  
   Obwohl Komponenten miteinander in Verbindung treten können, dürfen sie sich nicht direkt beeinflussen. Kommunikation zwischen zwei Komponenten geschieht über die genannten Interfaces zur Konfiguration einer Komponente.  
   Beim Erstellen von Komponenten sollte nicht zu sehr generalisiert werden, da sonst wieder _tight coupling_ entsteht:
   > Attempting to reuse too much code across components can increase their coupling. Isolation is more important than avoiding the repetition of superficially similar code.

4. **Soft encapsulation** (Einkapselung)
   Eine Komponente ist eine Black Box. Sie ist somit in sich geschlossen und darf nicht über offene Stellen andere Komponenten beeinflussen.


## Vorteile

Die Vorteile der Komponenten-Architektur lassen sich durch typische nichtfunktionale Anforderungen von Software bzw. anhand Eigenschaften der Softwarequalität (vgl. DIN 66272/ISO 9126/ISO 25000) aufzeigen:

- Skalierbarkeit: Es können nach belieben weitere Komponenten hinzugefügt werden, ohne dass dadurch andere Komponenten beeinflusst werden.
- Änderbarkeit: Änderungen an einer Komponente beeinflussen nicht andere Komponenten.
- Wartbarkeit: Fehlerursachen lassen sich auf eine Komponente zurückführen, da jede Komponente nur eine Aufgabe hat _(Single Responsibility principle)_.
- Testbarkeit: Komponenten können einzeln getestet werden, wodurch sich Tests einfacher schreiben lassen.

Weiter Vorteile während der Umsetzung sind:

- Zuverlässigere Zeitplanung durch definierte UI-Struktur.
- Mehrere Komponenten können zeitgleich von mehreren Entwicklern im Team programmiert werden, ohne dass dabei Friktion entsteht.

Zuletzt erlauben es Komponenten, einen _Living Styleguide_ bzw. eine _Pattern Library_ eines Projekts zu erstellen. Diese dienen der Dokumentation des Frontends und erfreuen sich an immer mehr Beliebtheit.

## Nachteile

Vor allem die Eigenschaften von CSS stehen mit den Merkmalen von Komponenten im Konflikt.

CSS ist vererbend: Regeln werden an andere Elemente vererbt, wenn sie nicht durch spezifischere Regeln überschrieben werden. Dies steht im Konflikt zur _Soft encapsulation_. Es lässt sich also nicht einfach verhindern, dass eine Komponente Regeln an verschachtelte Komponenten vererbt.

In den folgenden Kapiteln werden Möglichkeiten behandelt, um dies zu verhindern.

---

<sup>1</sup> Die in SUIT CSS definierten Merkmale "Cohesion" und "Documentation" wurden ausgelassen, da sie aus Sicht des Autors zur Definition von Komponenten nicht ausschlaggebend sind.  
"Cohesion" überschneidet sich stark mit "Composable and configurable" und "Loose coupling". Weiterhin ist die Dokumentation von Quellcode immer wichtig, nicht nur speziell für Komponenten.

---

## Quelle

- Gallagher, Nicolas; Twitter: _SUIT CSS design principles_, auf GitHub: https://github.com/suitcss/suit/blob/0cae87281148dbc77dfa3fa7639b2f8564bc945e/doc/design-principles.md
