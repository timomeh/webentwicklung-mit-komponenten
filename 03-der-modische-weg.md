# Der modische Weg

> **Rendering über JavaScript**

Wer schon selbst Webapplikationen programmiert hat – besonders vor ein paar Jahren – kennt folgendes Szenario: Der aktuelle Zustand der Website (engl. _state_) wird in HTML Elementen, deren Attributen oder Klassen im DOM gespeichert. Benötigt man Daten des aktuellen States, werden sie aus diesen Elementen, Attributen und Klassen ausgelesen.

Das ist keine solide Art, um Webapplikationen zu programmieren. Viele JavaScript-Frameworks haben es sich deshalb zur Aufgabe gemacht, dies zu verbessern. Ein Framework möchte ich hervorheben, da es die Komponenten-Architektur verfolgt: **[React](https://facebook.github.io/react/)**.

React wurde im Mai 2013 von Facebook veröffentlicht und erfreut sich seither an wachsender Beliebtheit. Neben Facebook, Instagram und WhatsApp wird React auch von Yahoo, Netflix und Airbnb verwendet, um nur wenige Firmen zu nennen.<sup>1</sup>

## Komponentenbildung in React

Eine React-Applikation besteht aus Komponenten, die in einer Baumstruktur angeordnet sind.

Eine Komponente sollte für eine einzige Aufgabe zuständig sein _(Modularity)_. Komponenten werden ineinander verschachtelt bzw. miteinander verbunden _(Composable)_. Komponenten werden nur über _Props_ (= Attribute der Komponente) konfiguriert _(Configurable)_, und können sich nicht auf eine andere Weise gegenseitig beeinflussen _(Loose coupling)_. Nach außen ist eine Komponente eine Black Box _(Soft encapsulation)_.<sup>2</sup>

All dies sind Merkmale, die wir von einer Komponente erwarten und die genau so in React implementiert wurden.

## Beispiel einer simplen Komponente in React

Das Beispiel des Artikels mit Autoren-Biographie aus Kapitel 2 kann in React folgendermaßen umgesetzt werden:

```JSX
// Definition
class Article extends React.Component {
  render () {
    return (
      <article className="Article">
        <h1 className="Article-headline">{this.props.headline}</h1>
        {this.props.content}
        <Author name={this.props.author.name} bio={this.props.author.bio} />
      </article>
    );
  }
}

class Author extends React.Component {
  render () {
    return (
      <div className="Article-aboutAuthor">
        <h2>{this.props.name}</h2>
        <p>{this.props.bio}</p>
      </div>
    )
  }
}

// Verwendung
const author = {
  name: 'Max Muster',
  bio: 'Excepteur sint occaecat cupidatat non proident.'
}

const content = <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.</p>

<Article headline="Lorem ipsum" author={author} content={content} />
```

Wenn wir die `<Article>` Komponente verwenden, müssen wir uns keine Gedanken um die `<Author>` Komponente machen. Die `<Article>` Komponente wird nur über Attribute _("Props")_ konfiguriert. Sie ist eine Instanz und bei Änderungen muss nur die Article-Class angepasst werden.

⚠️ **Achtung:** Damit wird aber auch die in Kapitel 2 behandelte _Seperation of Concerns_ verletzt. Das HTML ist fest mit JavaScript verbunden. Ohne JavaScript wird folglich kein HTML angezeigt.

## Wann soll ich React benutzen?

React eignet sich für komplexe Websites oder interaktive Webapplikationen. Für statische Seiten hat React einige Nachteile, die im Folgenden näher erläutert werden.

### Rendering

React berechnet den Quelltext der Seite komplett browserseitig. Der Quelltext, der vom Webserver geschickt wird, ist leer. Erst nachdem die Seite geladen wurde, wird JavaScript ausgeführt, somit der Quelltext berechnet und in den (noch) leeren DOM eingefügt.

Dadurch entsteht eine zusätzliche Ladezeit, in der der User keinen Inhalt sieht. Diese Ladezeit sollte man wenigstens mit einem Lade-Indikator dem User anzeigen. Laut Google verlassen 53% der mobilen Nutzer vorzeitig eine Seite, wenn sie innerhalb von 3 Sekunden nicht lädt.<sup>3</sup> Mobile Nutzer haben auch öfter instabile Internetverbindungen. **Die Technologie soll dem Endnutzer keinen Nachteil bringen.**

**Server-side Rendering:** React kann serverseitig gerendert werden, damit dem User direkt der Quelltext "pre-rendered" zugesendet wird. Das ist aber auch mit mehr Aufwand verbunden. Nicht nur muss der Server den React-Quellcode ausführen können – der Code muss auch so strukturiert sein, dass die Seite ohne JavaScript benutzbar ist. Letztendlich macht eine vorgerenderte Seite keinen Sinn, wenn man nicht mit ihr interagieren kann.

### Kann man auf JavaScript vertrauen?

Optimalerweise funktioniert eine Website auch ohne JavaScript. Es ist aber fast unmöglich, komplexere Webapplikationen auch ohne JavaScript benutzbar zu machen. Letztendlich kommt es auf die Audienz an, ob man die Website für Nutzer ohne JavaScript optimieren sollte. Mit 2.4% haben vergleichsweise viele Screenreader-Nutzer JavaScript deaktiviert.<sup>4</sup> 1.1% der sonstigen Nutzer haben JavaScript deaktiviert.<sup>5</sup>  
Die Frage, ob man jene Nutzer ignorieren sollte, muss selbst evaluiert werden.

Auch Suchmaschinen wie Google führen JavaScript aus, bevor eine Website interpretiert und indexiert wird. Informationen können also auch korrekt in einer Suchmaschine angezeigt werden.

Es gibt aber auch Crawler – hauptsächlich für Metadaten – die JavaScript nicht ausführen. Dazu gehören auch die Crawler von Facebook und Twitter. Solche Crawler sorgen dafür, dass zu einem Link weitere Informationen dargestellt werden können.  
Da in diesem Fall JavaScript nicht ausgefüllt wird, kann der Inhalt der Seite von Crawlern nicht interpretiert werden!

Metadaten im `<head>` sollten also auf jeden Fall ohne JavaScript verfügbar sein.

## CSS in React

In Kapitel 2 haben wir bereits besprochen, dass CSS der Bildung von Komponenten im Wege steht. Was wir bisher über React behandelt haben, löst dieses Problem noch nicht gänzlich.

### Inline Styles

Inline Styles sind CSS Regeln, die über das HTML `style` Attribut angewendet werden.

Im "traditionellen Weg" ist an Inline Styles gar nicht zu denken. Eine einzige Style-Änderung sorgt dafür, dass jedes Vorkommen der Komponente einzeln angepasst werden muss.

In React ist das aber kein Problem, da eine Komponente nur einmal definiert wird, und sonst tatsächlich instanziiert wird. Da diese Inline Styles direkt auf ein Element angewendet werden, vererben sie sich nicht auf andere Elemente.

Zur Verdeutlichung ein Beispiel einer Benachrichtigungs-Komponente, die eine erfolgreiche oder fehlgeschlagene Operation anzeigt.

```JSX
const mainStyle = {
  borderSize: '1px',
  borderStyle: 'solid',
  color: 'black'
}

const errorStyle = {
  borderColor: 'red',
  color: 'red'
}

const successStyle = {
  borderColor: 'green',
  color: 'green'
}

class Message extends React.Component {
  render () {
    let style
    if (this.props.type === 'error') style = {...mainStyle, ...errorStyle}
    if (this.props.type === 'success') style = {...mainStyle, ...successStyle}

    return <div style={style}>{this.props.text}</div>
  }
}


// Verwendung
<Message type="error" text="Es ist ein Fehler aufgetreten!" />
<Message type="success" text="Vorgang war erfolgreich." />
```

### CSS Modules

Eine weitere Möglichkeit sind [CSS Modules](https://github.com/css-modules/css-modules). Hier wird die CSS Datei über einen Build Prozess (Webpack) verarbeitet, sodass sie in JavaScript eingelesen werden kann. Dabei wird der CSS-Klassenname so verändert ist, dass er für jedes Element in der Komponente eindeutig wird. Die CSS Regeln bekommen somit tatsächlich einen _local scope_.

An dieser Stelle möchte ich nochmals auf _Seperation of Concerns_ hinweisen. Nun ist nicht nur HTML und JS fest miteinander verbunden, sondern auch noch CSS. Alle diese drei Bausteine einer Website, die mit dem Gedanken der Entkopplung konzipiert wurden, sind nun fest miteinander verbunden.


## Zusammenfassung

### Vorteile des "modischen Wegs"

- Echte Komponenten samt Instanziierung.
- _Encapsulation_ durch Möglichkeit von lokalem CSS Styling.
- Geeignet für komplexe Webapplikationen.

### Nachteile des "modischen Wegs"

- _Seperation of Concerns_ wird komplett aufgehoben.
- Lernbarriere: Neues Framework und Technologie, die zuerst gelernt werden muss.
- Ohne JavaScript ist die Website non-funktional.
- Ungeeignet für statische und kleine Websites.

---

- <sup>1</sup> Vgl. https://github.com/facebook/react/wiki/sites-using-react
- <sup>2</sup> Vgl. https://facebook.github.io/react/docs/components-and-props.html
- <sup>3</sup> Vgl. https://www.doubleclickbygoogle.com/articles/mobile-speed-matters/
- <sup>4</sup> Daten von Januar 2014, vgl. http://webaim.org/projects/screenreadersurvey5/
- <sup>5</sup> Daten von Oktober 2013, vgl. https://gds.blog.gov.uk/2013/10/21/how-many-people-are-missing-out-on-javascript-enhancement/
