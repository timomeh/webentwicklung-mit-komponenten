# Der traditionelle Weg

> **Decoupling von HTML, CSS und JS**

Diese Methode bezeichne ich als _traditionell_, da sie ohne neue Technologien, JavaScript-Frameworks oder -Libraries auskommt.

HTML, CSS und JS folgen dem Designprinzip der _Seperation of Concerns_.

- HTML beschreibt den Inhalt.
- CSS beschreibt die Präsentation.
- JavaScript beschreibt das Verhalten.

Somit sind alle 3 Bausteine voneinander entkoppelt. Genauer gesagt bauen sie aufeinander auf. Je nach User Agent (Browser, Textbrowser, Drucker, ...) werden jene Bausteine angezeigt oder ignoriert.<sup>1</sup>

Dieses Verhalten ist nicht als Nachteil zu sehen; es ist sogar sehr erstrebenswert. Es ist allerdings nicht einfach, jene Entkopplung in Komponenten wieder zusammenzuführen.

## Komponenten: Verbinden, was lose ist

Wie in [Kapitel 1](01-was-sind-komponenten.md) beschrieben, besteht eine Komponente aus HTML, CSS und JavaScript. Obwohl das Prinzip der _Seperation of Concerns_ weiterhin gilt, müssen trotzdem alle drei Bestandteile miteinander verbunden werden – und zwar so, dass sie nach außen hin ein gemeinsames Gebilde im Sinne einer _Black Box_ bilden.

Wichtigster Bestandteil der _traditionellen Methode_ sind Konventionen. Vor allem **Namenskonventionen** verbinden HTML-Klassennamen mit CSS-Selektoren. Oft wird dies auch _Methodik_ (_methodology_, vgl. BEM, SUIT CSS) oder _Architektur_ (_architecture_, vgl. SMACSS) genannt.

## Encapsulation in CSS

Ein großes Problem bei der Erstellung von Komponenten ist das Fehlen von nativer Einkapselung in CSS. Die meisten Konventionen verlangen daher, dass jedes Element einen eindeutigen Klassennamen bekommt. Damit soll verhindert werden, dass ungewollte Style-Regeln an Kinderelemente vererbt werden.<sup>2</sup>

Folgendes Beispiel, das oft in der Praxis zu finden ist, soll eine Schwierigkeit dabei aufzeigen:

```HTML
<article class="Article">
  <h1 class="Article-headline">Lorem ipsum</h1>
  <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.</p>
  <p>Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
</article>
```

```CSS
.Article { ... }
.Article-headline { ... }
.Article p { ... }
```

Solcher Quelltext wird oft von Content Management Systemen ausgegeben: Die `<p>`-Elemente haben keine Klassennamen. Eigentlich sieht dies nach einem simplen und sauberen Quellcode aus. Jedoch können sich die Style-Regeln von `.Article p` ungewollt an andere Kindelemente vererben:

```HTML
<article class="Article">
  <h1 class="Article-headline">Lorem ipsum</h1>
  <p> … </p>
  <p> … </p>

  <div class="Article-aboutAuthor">
    <p> … </p> <!-- Bio des Autors -->
  </div>
</article>
```

Hier vererben sich die Style-Regeln von `.Article p` auch auf den Fließtext innerhalb von `<div class="Article-aboutAuthor">…</div>`, was unter umständen nicht gewollt ist.

Dieses Problem ist nur sicher lösbar, wenn tatsächlich jedes Element, das mit styles versehen werden soll, einen unverwechselbaren Klassennamen erhält. Die Vererbung von CSS wird damit aufgehoben und die _encapsulation_ simuliert.

## Umsetzung

### BEM

Die weitaus bekannteste Methodik zur Umsetzung von Komponenten in CSS ist **[BEM](http://getbem.com/)**.<sup>3</sup> BEM bezeichnet Komponenten als _"Block"_.

BEM ist vor allem eine Namenskonvention mit dem Ziel, Klassennamen eindeutig und einzigartig zu machen.

- `.block`
- `.block__element`
- `.block__element--modifier`
- `.block--modifier`

Hier zeigt sich, dass ein wichtiger Bestandteil der Namensgebung die Trennung mit zwei Unterstrichen (`__`) und zwei Bindestrichen (`--`) ist.

**BEM benennt keine weiteren Konventionen.**

### SUIT CSS

[SUIT CSS](https://suitcss.github.io) ist eine von Twitter entwickelte Methodik zur Erstellung von Komponenten. Klassennamen sehen folgendermaßen aus:

- `.MyComponent`
- `.MyComponent--hasModifier`
- `.MyComponent.is-state`
- `.MyComponent-descendentElement`

Wie auch bei BEM stehen Trennstriche im Vordergrund. Außerdem spielt hier die Groß- und Kleinschreibung eine Rolle. Komponentennamen sind in PascalCase notiert, Elementnamen und Modifier in camelCase.

Im Gegensatz zu BEM gibt SUIT CSS noch weitere Restriktionen an, um alle Merkmale einer Komponente abzudecken:

- Modifier und State werden nur auf dem "Basiselement" der Komponente angewendet. Somit dienen dort die Klassennamen als Interface, um das Innere der Komponente zu steuern.
- Der Unterschied zwischen State und Modifier: **Modifier** bezeichnen verschiedene Arten einer Komponente, damit zu starke Repetition ähnlicher Komponenten vermieden wird. **State** ist der aktuelle Status der Komponente, der über JavaScript angesprochen wird.

## JavaScript in Komponenten

Wie genau JavaScript mit Komponenten verbunden wird, gibt weder BEM noch SUIT CSS vor. Dadurch bleibt es dem Entwickler offen, das Framework oder die Library seiner Wahl zu verwenden. Anders als sich an neue Namenskonventionen zu gewöhnen ist es schwerer, sich in festgelegte Frameworks einzuarbeiten.

## Don't repeat yourself

Nehmen wir an, es wurde eine Komponente in HTML, CSS und JS entwickelt. Wie implementieren wir sie an die Stellen, an denen sie benötigt wird?

Das größte Problem ist der HTML Code. In der Praxis habe ich viele Projekte gesehen, in denen HTML aus einer Komponentensammlung kopiert und an die benötigte Stelle eingefügt wird. Bei einer Änderung an der Komponente, muss jede Komponente einzeln angepasst werden.

Besser wäre eine Art _Instanziierung_ einer Komponente. Dies erreichen wir nur durch Template Engines bzw. Rendering Engines, Server-seitig oder Browser-seitig. Man darf nicht vergessen, dass Komponenten verschachtelbar sind, somit müssen auch jene Engines zulassen, dass solche Instanzen verschachtelt werden können.

Das nächste Kapitel knüpft an den Gedanken einer Rendering Engine an und beschäftigt sich mit dem Erstellen und Verwalten von Komponenten über JavaScript. _[Kapitel 3: Der modische Weg – Rendering über JavaScript](03-the-trendy-way.md)_

## Zusammenfassung

### Vorteile des "traditionellen Wegs"

- Grundgedanke der _Seperation of Concerns_ wird beibehalten, wobei die Klassennamen als Schnittstelle zwischen HTML, CSS und JS dienen.
- _Encapsulation_ von Komponenten.
- CSS wird automatisch stukturierter.
- Namenskonventionen sind Code Guidelines für Projekte mit mehreren Entwicklern.


## Nachteile des "traditionellen Wegs"

- Keine echte _Encapsulation_; Vererbung wird nur umgangen.
- Klassennamen können in manchen Fällen repetitiv werden (siehe Beispiel oben).
- Problematisch DRY zu bleiben; Rendering Engines müssen hinzugezogen werden.

---

- <sup>1</sup> Vgl. [Stefanov 2010, 183f]
- <sup>2</sup> Vgl. https://github.com/suitcss/suit/blob/0cae87281148dbc77dfa3fa7639b2f8564bc945e/doc/design-principles.md
- <sup>3</sup> Vergleich zwischen den Suchbegriffen "bem css", "smacss" und "suitcss" auf Google. Zeigt signifikante Beliebtheit von BEM ab Mitte 2015. https://www.google.de/trends/explore?date=all&q=bem%20css,smacss,%22suit%20css%22

---

## Literatur

- **Stefanov 2010**  
  Stefanov, Stojan: _JavaScript Patterns. Build Better Applications with Coding and Design Patterns_, deutsche Ausgabe, O'Reilly Verlag GmbH & Co. KG, Köln 2010
