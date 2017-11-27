# CSS Best Practices

Um uns das Leben beim Schreiben von CSS leicht zu machen, sollte die Spezifität so niedrig wie möglich gehalten werden. Folgende Regeln helfen dabei:

* keine Verwendung von IDs
* Verschachtelung von Selektoren auf ein Minimum reduzieren
* Vermeidung einer Überspezifizierung
* auf eine Verkettung von Selektoren verzichten

## Selektorregeln

### Vermeide zu generische Selektoren

Um den Code einfacher erweitern und besser warten zu können, ist ein gezieltes Styling von Elementen immens wichtig. Die Faustregel dazu: Sobald ein Element gestylet werden soll, setze eine Klasse.

**schlecht**

``` sass
header
  padding: 10px 20px

h2
  margin: 0

ul
  list-style-type: circle
```

**gut**

``` sass
.page-header
  padding: 10px 20px

.subtitle
  margin: 0

.link-list
  list-style-type: circle
```

### Vermeide Basiselementselektoren

Basiselementselektoren sind schlecht (siehe Regel darüber) und erhöhen die Spezifität unnötigerweise. Auch hier sollte auf eine Klasse zurückgegriffen werden. Außerdem sollten Elemente wie `<div>` und `<span>` im Markup immer ein Klassenattribut besitzen. Andernfalls wären sie sowieso überflüssig.

**schlecht**

``` sass
.navigation div
.navigation span
.navigation ul
```

**gut**

``` sass
.navigation--box
.navigation--title
.navigation--list
```

### Vermeide eine Überspezifizierung durch Basiselementselektoren

Eine Überspezifizierung durch Basiselementselektoren hat zur Folge, dass der CSS-Code ans Markup gekoppelt wird. Würde man das Element im Markup ändern, z.B. aus einer `h2` eine `h3` machen, müsste man dies auch im Stylesheet tun, was einen erhöhten Wartungsaufwand nach sich zieht. Obendrein wird die Spezifität unnötigerweise erhöht.

**schlecht**

``` sass
ul.link-list
div.example
a.back
```

**gut**

``` sass
.link-list
.example
.back
```

### Vermeide Nachfahrenselektoren

Der Nachfahrenselektor ist der teuerste Selektor in CSS. Gib dem entsprechenden Element besser eine Klasse und style es direkt.

**schlecht**

``` sass
.link-list li a
```

**gut**

``` sass
.link-list--link
```

### Vermeide den selben Selektor fürs Styling (CSS) und fürs Verhalten (JS) zu verwenden

Separation of concerns. Möchte man Javascript auf ein Element anwenden, hat es sich etabliert, hierfür ein `data-*`Attribut zu nutzen. Der Vorteil: Ändert man später nochmal das Klassenattribut, bleibt das Javascript weiterhin in Takt.

**schlecht**

``` sass
.dialog-opener
```

``` js
$('.dialog-opener')
```

**gut**

``` js
$("[data-role='dialog-opener']")
```

## Coding Konventionen

### Benutze die Kurzschreibweise

Wenn man alle Werte einer Eigenschaft auf einmal angibt, ist die Kurzschreibweise schneller zu erfassen und spart obendrein ein paar Bytes ein.

**schlecht**

``` sass
.box
  padding-top: 0
  padding-right: 10px
  padding-bottom: 20px
  padding-left: 10px
```

**gut**

``` sass
.box
  padding: 0 10px 20px
```

### Gib nur die Werte an, die du auch setzen willst

Wenn man zu viele Werte auf einmal angibt, könnte es sein, dass man versehentlich einen vererbten Wert überschreibt. Außerdem muss man diesen Wert dann u.U. später nochmal überschreiben, weil er sich wiederum selbst weitervererbt hat.

**schlecht**

``` sass
.box
  padding: 0 10px 0 0
  background: rgba(0, 0, 0, 0.5)
```

**gut**

``` sass
.box
  padding-right: 10px
  background-color: rgba(0, 0, 0, 0.5)
```

### Vermeide die Einheitsangabe nach dem Wert "0"

Null ist Null. :)

**schlecht**

``` sass
.box
  margin: 0px
```

**gut**

``` sass
.box
  margin: 0
```

**Ausnahme dort, wo sie nicht weggelassen werden darf**

``` sass
.box
  transform: rotate(0deg)
```

### Nutze 100–900 bei der Angabe der Schriftdicke

Die Zahlen 100, 200 … 900 sind typografischer Standard und erlauben es uns, differenzierter mit Abstufungen der Schriftdicke umzugehen als es _normal_ und _bold_ tun.

**schlecht**

``` sass
.box
  font-weight: bold
```

**gut**

``` sass
.box
  font-weight: 700
```
