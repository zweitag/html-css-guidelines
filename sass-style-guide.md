# Sass Styleguide

## Inhaltsverzeichnis

- [Einleitung](#einleitung)
- [Manifest-Datei](#manifest-datei)
- [Style-Definitionen](#style-definitionen)
- [Syntax und Formatierung](#syntax-und-formatierung)
- [Selektorregeln](#selektorregeln)
- [Coding Konventionen](#coding-konventionen)
- [Kommentare](#kommentare)
- [Linting](#linting)
- [EditorConfig](#editorconfig)

## Einleitung

Bei der Arbeit an großen, langlebigen Projekten mit mehreren Entwicklern ist es wichtig, dass wir alle einheitlich arbeiten. Unsere oberste Prämisse sollte sein, dass unsere Stylesheets auf der einen Seite leicht pflegbar und skalierbar sind und auf der anderen Seite unser Code transparent und lesbar bleibt. Um dies zu erfüllen, helfen folgende Empfehlungen.

## Manifest-Datei

Wir splitten unseren Sass-Code in viele kleine Partials auf, die thematisch in sich geschlossen sind. Das erhöht die Flexibilität, Lesbarkeit und Wartbarkeit. In unserer Manifest-Datei ordnen wir unsere Partials dann von generisch hin zu spezifisch.

``` sass
// Reset Styles
//-------------
@import globals/reset

// Libary Imports
//---------------
//@import libs/foundation

// Utility Imports
//----------------
@import utilities/center
@import utilities/visually-hidden

// Global Imports
//---------------
@import globals/colors
@import globals/typography

// Base Styles
//------------
@import globals/base

// Abstract Component Imports
//---------------------------
@import abstract-components/boundary-box
@import abstract-components/gradient-overlay

// Generic Component Imports
//--------------------------
@import generic-components/button
@import generic-components/data-table

// Specific Component Imports
//---------------------------
@import specific-components/company-search
@import specific-components/page-header
```

## Style-Definitionen

Wann wird wo was definiert? Wir gehen inhaltsgetrieben vor, d.h. wir schreiben unseren Sass-Code in der Reihenfolge, wie die Elemente auf der gerenderten Seite erscheinen werden:

1. **von außen nach innen**: ZASAF folgend beginnt man in der Regel mit dem Page-Wrapper-Element.
2. **von oben nach unten**: Danach unterteilt man sich die Seite in (horizontale) Sektionen und beginnt mit der ersten (page-header -> page-main -> page-footer).
3. **von links nach rechts**: In einer Sektion fängt man ganz links mit dem ersten Element an (Page-Header: Logo links -> Navigation in der Mitte -> Suche rechts).

### Notationsreihenfolge innerhalb eines Elements

Verschachtelungen innerhalb eines Elements beginnen mit Pseudoklassen, gefolgt von Pseudoelementen, gefolgt von Modifiern. Auf Kindelemente, sowohl direkte als auch tiefer im DOM verschachtelte, sollte weitestgehend verzichtet werden. Hierfür wäre es besser einen neuen Selektorblock zu erstellen (siehe Coding Konventionen unten).

``` sass
.main-navigation--item
  position: relative
  &:hover
    border-color: $color-border-loud
  &::before
    content: '*'
  &.highlighted
    bacground-color: $color-background-loud

.main-navigation--link
  display: block
  padding: 5px 0
  .highlighted > &
    color: $color-text-inverted
```

## Syntax und Formatierung

Die Basis eines gemeinsam gelebten Stils ist ein grundlegendes Set an Syntax- und Formatierungsregeln. Eine strikte Einhaltung bringt Effizienzpotenziale, weil man Code schneller lesen, verstehen und abgleichen kann. Es ist weniger wahrscheinlich, dass man Dinge übersieht oder missversteht und niemand verbringt Zeit damit, seinen Arbeitsbereich durch Umformatierungen »für sich einzurichten«.

### Schreibweise

- Einrückung: zwei Leerzeichen, keine Tabs
- Multi-Line-Stil: Ein Selektor pro Zeile, eine Deklaration pro Zeile
- Selektorverschachtelung: maximal drei Ebenen tief, nach Möglichkeit jedoch vermeiden und eine eigene Klasse für Elemente einer Komponente einführen
- Alle Bezeichnungen werden auf englisch und klein geschrieben. Setzt man mehrere Wörter zu einem Selektor zusammen, trennt diese ein Bindestrich: `.data-table`.
- Wir verwenden einfache Anführungszeichen: `font-family: 'Andale Mono'`.
- Werte in Attributselektoren werden in Anführungszeichen gesetzt: `input[type='checkbox']`.
- Nach einem `:` hinter der CSS-Eigenschaft gehört ein Leerzeichen: `margin: 0`
- Nach einem `,` in einer kommaseparierten Liste gehört ein Leerzeichen: `rgba(255, 255, 255, 0.5)`
- Wir verwenden rgb und rgba Farbcodes, weil sich die meisten von uns vorstellen können, welche Farbe durch das Mischen von Rot, Grün und Blau zustande kommt.

### Deklarationsreihenfolge

Um nicht ständig nach einer CSS-Eigenschaft suchen zu müssen, verwenden wir eine feste Deklarationsreihenfolge. Sie ist in logische Blöcke gegliedert, die man sich im Detail in der Linter-Config (TODO: verlinken) ansehen kann.

#### 1. Übergeordnetes

- z.B. content, will-change

#### 2. Allgemeine Sichtbarkeit

- z.B. opacity, visibility, display

#### 3. Layout-Mechanismen

- Flexbox: z.B. justify-content, align-items, flex
- Grid: z.B. columns, column-gap, column-rule
- Table: z.B. table-layout, border-collapse, border-spacing
- List: z.B. list-style, counter-increment, counter-reset

#### 4. Positionierung im Raum

- z.B. float, position, vertical-align

#### 5. Box Model inkl. Transformation

- z.B. box-sizing, width, height, margin, border, padding, transform

#### 6. Typografie

- z.B. font, line-height, text-decoration, white-space, word-wrap

#### 7. Farbe

- z.B. fill, color, background

#### 8. Rest

- z.B. cursor, pointer-events, transition

## Selektorregeln

### Vermeide zu generische Selektoren

In 99% der Fälle wird man generische Selektoren überschreiben (müssen), was zu mehr Code und einem erhöhtem Wartungsaufwand führt. Darum sei spezifischer und benutze stattdessen besser eine Klasse. (Ausnahme: CSS-Resetstyles)

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

Separation of concerns. Wenn du den Klassennamen fürs Styling später nochmal änderst, bleibt dein Javascript nachwievor in Takt.

**schlecht**

``` sass
.dialog-opener
```

``` js
$('.dialog-opener')
```

**gut**

Benutze ein `data`-Attribut:

``` js
$('[data-dialog-opener]')
```

Oder benutze eine ID:

``` js
$('#dialog-opener')
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

## Kommentare

Da man bei CSS nicht immer sofort sieht, warum eine Deklaration eingesetzt wird, macht es durchaus Sinn, Code zu kommentieren. Durch die Verwendung von Sass greifen wir dabei auf stille Kommentare zurück, die mit zwei Slashes `// …` eingeleitet werden. In der Regel sind Kommentare nur für die Entwickler wichtig und müssen dadurch nicht im CSS ausgegeben werden. Das reduziert zudem die Dateigröße.

Möchte man eine einzelne Zeile in einem Block kommentieren, so setzt man den Kommentar ans Ende der Deklaration:

``` sass
.box
  position: absolute
  top: -12px // Fallback if JS fails to load
```

Möchte man dagegen einen kompletten Block beschreiben, so setzt man den Kommentar direkt darüber:

``` sass
// Global Styles
.box
  position: relative

// Media Queries
+respond-to(large)
  .box
    margin-top: 20px

+respond-to(medium)
  .box
    margin-top: 10px

+respond-to(small)
  .box
    margin-top: 5px
```

## Linting

Zum Testen unseres Sass-Codes benutzen wir [Sass Lint](https://github.com/sasstools/sass-lint). Der Linter sollte am Ende keine Fehler oder Warnungen ausspucken.

## EditorConfig

Um unschöne Diffs zu vermeiden, sollte man sich seinen Editor folgendermaßen einrichten:

``` shell
# editorconfig.org

[*.{sass,scss}]
charset = utf-8
end_of_line = lf
indent_size = 2
indent_style = space
insert_final_newline = true
trim_trailing_whitespace = true
```
