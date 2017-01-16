# Der native Weg

> **Web Components als Browsertechnologie**

Die Technologie der Web Components wurde von der W3C zuerst im Jahre 2012 vorgestellt. Bei Web Components selbst handelt es sich um *keine Spezifikation*. Stattdessen sind Web Components das Resultat aus folgenden vier Spezifikationen:

- Custom Elements
- Shadow DOM
- Templates
- HTML Imports

Die Spezifikationen sind noch nicht final und werden ständig überarbeitet. Da es sich um eine W3C Spezifikation handelt, stehen die Chancen sehr hoch, dass alle modernen Browser diese Spezifikation implementieren. Schon jetzt sind Web Components nativ in manchen Browsern verfügbar – auch wenn sich die APIs noch oft ändern werden.<sup>1</sup>

Der Polyfill [webcomponentsjs](https://github.com/webcomponents/webcomponentsjs) erlaubt die Verwendung von Web Components schon jetzt in allen gängigen Browsern.

## Was sind Web Components?

Web Components erlauben es, eigene HTML Elemente über eine JavaScript API definieren zu können.

Am Beispiel des `<video>` Elements kann bei Web Components ebenso folgendes erreicht werden:

- Ein Template verwenden: Ein Video Player besitzt das Video, Lautstärke-Regler, Dauer der Anzeige, Play/Pause-Button, …
- Öffentliche JS-Methoden und Events definieren: `video.play()`, `video.pause()`, `video.addEventListener('canplay')`, …
- Auf Attribute reagieren: `<video autoplay loop></video>`
- Das Innere verstecken: Auch im Elementinspektor kann nicht nachvollzogen werden, wie das Video tatsächlich in den Browser kommt.
- Elemente miteinander verschachteln: Innerhalb des `<video>` Elements ist auch ein `<source>` Element zu finden.

Vergleichen wir dies mit den Merkmalen einer Komponente auf Kapitel 1, so sind auch bei Web Components all diese Merkmale zu finden.

## Spezifikationen

### Custom Elements

Mithilfe von _Custom Elements_ können eigene HTML Elemente über JavaScript definiert werden. Custom Elements müssen mindestens ein Minuszeichen (-) im Tag-Namen besitzen. `<slider>` wäre also kein gültiger Tag, `<my-slider>` wäre gültig. Tags ohne Minus werden vom Browser direkt als `HTMLUnknownElement` interpretiert. Tags mit Minus werden vom Browser – auch wenn er das JavaScript dazu noch nicht geladen hat! – direkt als `HTMLElement` interpretiert.<sup>2</sup>

Folgender Code reicht schon, um das Custom Element `<my-slider>` zu definieren.

```js
class MySlider extends HTMLElement { }
customElements.define("my-slider", MySlider)
```

Anstatt das `HTMLElement` als Interface zu nutzen, können auch spezifischere Elemente genutzt werden. Möchte man einen eigenes Button-Element definieren, so empfiehlt sich das `HTMLButtonElement`. Eine Liste aller Interfaces ist hier zu finden: [https://html.spec.whatwg.org/multipage/indices.html#element-interfaces](https://html.spec.whatwg.org/multipage/indices.html#element-interfaces)


#### Erweitern von nativen HTML Elementen

In bestimmten Elemente sind nur verschiedene Kinderelemente erlaubt. Beispielsweise kann man in einer Tabelle kein eigenes `<tr>` Element wie `<my-custom-tr>` verwenden. Beim Parsen des Dokuments wird der Browser das Element außerhalb der `<table>` positionieren.

```html
<table>
	<tr>...</tr>
	<my-custom-tr>...</my-custom-tr> <!-- nicht erlaubt! -->
	<tr>...</tr>
	<tr>...</tr>
</table>
```

Um trotzdem das gewünschte Ergebnis zu erzielen, kann man native HTML Tags mit dem `is`-Attribut erweitern.

```html
<table>
	<tr>...</tr>
	<tr is="my-custom-tr">...</tr> <!-- erlaubt -->
	<tr>...</tr>
	<tr>...</tr>
</table>
```

Ein weiterer Anwendungsfall wäre Zugänglichkeit (_"accessibility"_). Wer zum Abschicken eines Formulars einen `<special-submit>` Button benutzen möchte, kommt möglicherweise in Probleme mit Screenreadern oder anderen Hilfsmitteln für Menschen mit Einschränkungen. Anstatt des `<special-submit>` würde sich auch hier ein `<button is="special-submit">` empfehlen.

Native HTML Elemente können in Custom Elements folgendermaßen als Interface verwendet werden:

```js
class SpecialSubmit extends HTMLButtonElement { }
customElements.define("special-submit", SpecialSubmit, { extends: "button" })
```

#### Lifecycle

Ein Custom Element besitzt einen Lifecycle, der über JavaScript aufrufbar ist.

```js
class MySlider extends HTMLElement {
  constructor() {
    super();
    // Ein neues Element des Typs MySlider wurde erstellt
  }

  connectedCallback() {
    // Dieses Element wurde dem DOM hinzugefügt
  }

  disconnectedCallback() {
    // Dieses Element wurde vom DOM entfernt
  }

  attributeChangedCallback(attrName, oldValue, newValue) {
    // Ein Attribut des Elements hat sich verändert, wurde hinzugefügt oder entfernt
  }
}
```

Man kann sich nun fragen, wann `constructor` und wann `connectedCallback` verwendet werden sollte. Das Erstellen des Elements resultiert in den meisten Fällen auch darin, dass das Element dem DOM hinzugefügt wurde. Soll nun das Element im `constructor` oder im `connectedCallback` eingerichtet werden?

Die HTML Spec gibt dazu eine Antwort:<sup>3</sup>

> - In general, work should be deferred to connectedCallback as much as possible – especially work involving fetching resources or rendering. However, note that connectedCallback can be called more than once, so any initialization work that is truly one-time will need a guard to prevent it from running twice.
> - In general, the constructor should be used to set up initial state and default values, and to set up event listeners and possibly a shadow root.

### Shadow DOM

Der Shadow DOM ist ein Teil des DOMs, der "im Schatten liegt"; er ist also entweder nur explizit von außen aufrufbar (mode: open) oder sogar gar nicht sichtbar (mode: closed).

Vor allem hilft der Shadow DOM aber auch bei CSS! Das CSS, was innerhalb eines Shadow DOMs definiert wurde, ist auch nur innerhalb dieses Shadow DOMs gültig. **Es ist ein echter, nativer, _local scope_ in CSS.**

Der Shadow DOM kann unkompliziert einem Element hinzugefügt werden und besitzt die üblichen Methoden (`innerHTML`, `appendChild`, …).<sup>4</sup>

```js
const someElement = document.querySelector('.some-element')
const shadowRoot = someElement.attachShadow({mode: 'open'})

shadowRoot.innerHTML = `<style>
p { color: red }
</style>
<p>Dieser Text steht im Shadow DOM, ist rot und sorgt nicht dafür, dass anderer Text auch rot wird.</p>`
```

### Templates

Im Shadow DOM haben wir gerade `innerHTML` als String definiert. Wir möchten das HTML im Shadow DOM aber auch als HTML definieren, nicht als String. Das `<template>` Element ermöglichen uns dies.

Das `<template>` Element wird vom Browser initial nur geparst, nicht gerendert und somit nicht angezeigt. Um angezeigt zu werden, muss es dupliziert werden und explizit mit JavaScript dem DOM hinzugefügt werden.<sup>5</sup>

```html
<template id="someElementInner">
  <style>p { color: red }</style>
  <p>Dieser Text steht im Shadow DOM, ist rot und sorgt nicht dafür, dass anderer Text auch rot wird.</p>
</template>
```

```js
const someElement = document.querySelector('.some-element')
const shadowRoot = someElement.attachShadow({mode: 'open'})

const template = document.querySelector('#someElementInner')
const templateDuplicate = document.importNode(template.content, true)

shadowRoot.appendChild(templateDuplicate)
```

### HTML Imports

**Eine Web Component ist eine HTML-Datei.** Keine JavaScript Datei, auch nicht mehrere Dateien. _Eine_ HTML-Datei enthält ein `<template>` und ein `<script>`, in dem das JavaScript zur Web Component notiert ist.

Diese HTML-Datei muss importiert werden, damit der Browser das Custom Element registrieren kann. Dafür gibt es HTML Imports.

HTML Imports werden als `<link>` Element im `<head>` unserer eigentlichen Website eingebunden, ähnlich wie wir auch CSS-Dateien importieren.<sup>6</sup>

```HTML
<head>
  ...
  <link rel="import" href="my-slider.html">
</head>
```

Mehrere Web Components werden ganz einfach über mehrere link-Elemente eingebunden.

```HTML
<head>
  ...
  <link rel="import" href="my-slider.html">
  <link rel="import" href="some-element.html">
  <link rel="import" href="special-submit.html">
  <link rel="import" href="my-custom-tr.html">
</head>
```

**Erhöht es nicht die Ladezeit, wenn viele Dateien geladen werden müssen?**

Ja und Nein.

Aktuell: ja. Bei HTTP 1.1 sollen so wenig Requests wie möglich gemacht werden. Jeder Request stößt einen komplett neuen HTTP Request Verlauf an. Bei Web Components hilft [polymer-bundle](https://github.com/Polymer/polymer-bundler) dabei, viele HTML Imports in einer Datei zusammenzufügen.

In Zukunft: nein. Durch HTTP/2 werden viele Optimierungen, die wir wegen HTTP 1.1 angewendet haben, zum Anti-Pattern. Wir können sorgenfrei viele Requests machen und viele, kleinere Dateien ausliefern, was sogar zum Vorteil wird.<sup>7</sup>

## Beispiel einer Web Component

Stellen wir uns einen Slider als Web Component vor. Der Slider ist eine Liste von Bildern, es wird jeweils nur ein Bild angezeigt, und mit einem Button kann man das nächste bzw. vorige Bild anzeigen lassen. Eine Web Component für ein eigenes Slider-Element, `<my-slider>` bzw. my-slider.html, könnte wie folgt aussehen:

```html
<template>
  <style>
    button { position: absolute; top: 50% }
    .next-button { right: 0 }
    .prev-button { left: 0 }
    img { display: none }
    img.active { display: block }
  </style>
  <button class="next-button">Nächstes Bild</button>
  <div class="images">
    <slot></slot>
  </div>
  <button class="prev-button">Voriges Bild</button>
</template>

<script>
  ((window, document) => {
    // "document" ist das document in der Datei, in der diese Web Component eingebunden wird. Wir benötigen außerdem noch das "document" _dieser Datei_, also der Web Component:
    const thisDocument = (document._currentScript || document.currentScript).ownerDocument

    const tmpl = thisDocument.querySelector('template')

    class MySlider extends HTMLElement {
      constructor () {
        super();

        const shadowRoot = this.createShadowRoot()
        const tmplDuplicate = document.importNode(tmpl, true)
        shadowRoot.appendChild(tmplDuplicate)

        this.images = shadowRoot.querySelector('.images')
        this.nextBtn = shadowRoot.querySelector('.next-button')
        this.prevBtn = shadowRoot.querySelector('.prev-button')
      }

      connectedCallback () {
        this.nextBtn.addEventListener('click', () => {
          this.showNextImage()
        })

        this.prevBtn.addEventListener('click', () => {
          this.showPrevImage()
        })
      }

      showNextImage () {
        // ...
      }

      showPrevImage () {
        // ...
      }
    }
  })
</script>
```

Eingebunden in einer Website könnte unser Slider-Element folgendermaßen verwendet werden:

```HTML
<!DOCTYPE html>
<html>
  <head>
    <link rel="import" href="my-slider.html">
  </head>
  <body>
    <my-slider>
      <img src="image1.jpg" class="active">
      <img src="image2.jpg">
      <img src="image3.jpg">
    </my-slider>

    <script>
      // Wir können sogar hier per JavaScript auf die Methoden der
      // Web Component zugreifen!
      document.querySelector('my-slider').showNextImage()
    </script>
  </body>
</html>
```

## Zusammenfassung

### Vorteile des "nativen Wegs"

- Echte Komponenten samt Instanziierung.
- **Echte** _Encapsulation_ durch natives, lokales CSS Styling.
- Geeignet für kleine und große Websites oder Webapplikationen.
- Native Technologie, kein Framework.
- Vorgefertigte Komponenten können sehr einfach eingefügt werden.

### Nachteile des "nativen Wegs"

- Noch keine finale Spezifikation.
- Bis HTTP/2 noch Konkatenation von Dateien.
- Angewiesen auf JavaScript.

---

- <sup>1</sup> Vgl. https://www.w3.org/TR/2012/WD-components-intro-20120522/
- <sup>2</sup> Vgl. https://developers.google.com/web/fundamentals/getting-started/primers/customelements
- <sup>3</sup> Vgl. https://w3c.github.io/webcomponents/spec/custom/#custom-element-conformance
- <sup>4</sup> Vgl. https://developers.google.com/web/fundamentals/getting-started/primers/shadowdom
- <sup>5</sup> Vgl. https://developer.mozilla.org/en-US/docs/Web/Web_Components/Shadow_DOM
- <sup>6</sup> Vgl. https://www.w3.org/TR/html-imports/#link-type-import
- <sup>7</sup> Vgl. https://www.smashingmagazine.com/2016/02/getting-ready-for-http2/
