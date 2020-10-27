# Sass Styleguide

## Inhaltsverzeichnis

- [Einleitung](#einleitung)
- [Manifest-Datei](#manifest-datei)
- [Style-Definitionen](#style-definitionen)
- [Syntax und Formatierung](#syntax-und-formatierung)
- [Mixins vs. Extends](#mixins-vs-extends)
- [Media Queries](#media-queries)
- [Kommentare](#kommentare)
- [Linting](#linting)
- [EditorConfig](#editorconfig)

## Einleitung

Bei der Arbeit an großen, langlebigen Projekten mit mehreren Entwicklern ist es wichtig, dass wir alle einheitlich arbeiten. Unsere oberste Prämisse sollte sein, dass unsere Stylesheets auf der einen Seite leicht pflegbar und skalierbar sind und auf der anderen Seite unser Code transparent und lesbar bleibt. Um dies zu erfüllen, helfen folgende Guidelines.

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
  &.is-highlighted
    background-color: $color-background-loud

.main-navigation--link
  display: block
  padding: 5px 0
  .main-navigation--item.is-highlighted &
    color: $color-text-inverted
```

## Syntax und Formatierung

Die Basis eines gemeinsam gelebten Stils ist ein grundlegendes Set an Syntax- und Formatierungsregeln. Eine strikte Einhaltung bringt Effizienzpotenziale, weil man Code schneller lesen, verstehen und abgleichen kann. Es ist weniger wahrscheinlich, dass man Dinge übersieht oder missversteht und niemand verbringt Zeit damit, seinen Arbeitsbereich durch Umformatierungen »für sich einzurichten«.

### Sass oder SCSS

Grundsätzlich lieben wir prägnanten Code, d.h. Klammern und Semikolons betrachten wir als visuelles Rauschen. Im Zusammenspiel mit Haml benutzen wir daher die *Sass-Syntax*, da sie sehr homogen dazupasst. Wir verstehen aber auch, dass ein Umgewöhnen mit Kosten verbunden ist und wenn der Haml-Vorteil wegfällt, können die initialen Entwickler auf dem Projekt nach ihrer Präferenz entscheiden. So ist es z.B. bei der Entwicklung einer React-App Usus, die *SCSS-Syntax* zu verwenden.

### Schreibweise

- Einrückung: zwei Leerzeichen, keine Tabs
- Multi-Line-Stil: Ein Selektor pro Zeile, eine Deklaration pro Zeile
- Selektorverschachtelung: maximal drei Ebenen tief, nach Möglichkeit jedoch vermeiden und eine eigene Klasse für Elemente einer Komponente einführen
- Alle Bezeichnungen werden auf englisch und klein geschrieben. Setzt man mehrere Wörter zu einem Selektor zusammen, trennt man diese durch einen Bindestrich: `.data-table`.
- Wir verwenden einfache Anführungszeichen: `font-family: 'Andale Mono'`.
- Werte in Attributselektoren werden in Anführungszeichen gesetzt: `input[type='checkbox']`.
- Nach einem `:` hinter der CSS-Eigenschaft gehört ein Leerzeichen: `margin: 0`
- Nach einem `,` in einer kommaseparierten Liste gehört ein Leerzeichen: `rgba(255, 255, 255, 0.5)`
- Wir verwenden rgb und rgba Farbcodes, weil sich die meisten von uns vorstellen können, welche Farbe durch das Mischen von Rot, Grün und Blau zustande kommt.

### Deklarationsreihenfolge

Um nicht ständig nach einer CSS-Eigenschaft suchen zu müssen, verwenden wir eine feste Deklarationsreihenfolge. Sie ist in logische Blöcke gegliedert, die man sich im Detail in der [Linter-Config](https://github.com/zweitag/rails-project-template/blob/master/.sass-lint.yml) ansehen kann.

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

## Mixins vs. Extends

Wir arbeiten ausschließlich mit Mixins, [da Extends einige Nachteile mit sich bringen.](https://www.sitepoint.com/avoid-sass-extend/)

## Media Queries

Um Inhalte optimal für verschiedene Devices und den damit verbundenen Viewports bereitzustellen, setzen wir Media Queries ein. Diese werden mit Hilfe des `respond-to` Mixins in jeder einzelnen Komponente in einem Block am Ende deklariert. Jede Viewport-Range ist dabei in sich geschlossen, d.h. es werden keine Styles aus den anderen Media Queries überschrieben:

```
<--- large --- | --- medium --- | --- small --->
```

Alle Media Queries sind gleichwertig, es gibt kein *Desktop first* oder *Mobile first*. Existieren Styles, die übergreifend für alle Media Queries gleich sind, werden diese oben im Stylesheet festgehalten. Der Dateiaufbau sieht demnach wie folgt aus:

``` sass
// Global Styles
.box
  …

// Media Queries
+respond-to(large)
  .box
    …

+respond-to(medium)
  .box
    …

+respond-to(small)
  .box
    …
```

**Vorteile:**

- **Effizienz** – Styling-Anpassungen sind schneller und gezielter zu bewerkstelligen. Tritt z.B. auf Smartphone XY ein Darstellungsfehler auf oder man möchte ein Element für die kleinste Viewport-Range optimieren, dann steuert man direkt den Block unter `+respond-to(small)` an und macht dort seine Änderungen.
- **Leserlichkeit** – Dadurch, dass alle Media Query Definitionen en bloc am Ende stehen, sind sie nicht nur leichter aufzufinden, das Stylesheet bleibt obendrein auch leserlich. Würde man die Media Queries direkt ans Element schreiben, enstünde durch die ständige Einrückung im Code ein stockender Lesefluss.
- **Wartbarkeit** – Bei dieser Vorgehensweise ist zudem mit weniger ungewollten Seiteneffekten zu rechnen. Es können keine Fehler in anderen Media Queries entstehen, da diese voneinander abgeschottet sind.

**Nachteile:**

- Es könnte der Eindruck enstehen, dass Code dupliziert bzw. wiederholt geschrieben und somit gegen das DRY-Prinzip verstoßen wird, weil anstatt von zwei Definitionen (global plus einmal überschreiben) nun immer drei (alle Media Queries) gesetzt werden müssen. Dem ist jedoch nicht so! Die Erfahrung zeigt, dass eine Änderung in nur einer Media Query eher die Seltenheit ist. In der Regel werden zwei oder gleich alle drei angepasst. Sollte dies dennoch mal passieren, sind die zuzätzlichen Bytes locker zu verkraften.

### Vorgehensweise

Schritt eins, man definiert seine `$breakpoints` in den entsprechenden [Settings](https://github.com/zweitag/rails-project-template/blob/master/app/assets/stylesheets/utilities/_responsify.css.sass). **Hinweis:** Auf mehr als drei Media Queries sollte man nicht zurückgreifen, da dies den Komplexitätsgrad und den damit verbundenen Zeitaufwand enorm in die Höhe treibt.

Beginnt man nun damit die Komponente grundlegend zu stylen, wandern erst einmal alle Style-Definitionen in den globalen Bereich.

``` sass
// Global Styles
.box
  position: relative
  margin-top: 20px
  color: $accent-color
```

Möchte man eine neue Eigenschaft für eine bestimmte Viewport-Range hinzufügen, so setzt man diese in der entsprechenden Media Query.

``` sass
// Global Styles
.box
  position: relative
  margin-top: 20px
  color: $accent-color

// Media Queries
+respond-to(large)
  .box
    border: 1px solid $border-color
```

Für den Fall, dass man eine bereits global gesetzte Eigenschaft für eine bestimmte Media Query ändern möchte, überschreibt man den globalen Style *nicht*, sondern entfernt ihn aus dem globalen Scope und setzt ihn gleichzeitig für alle Media Queries.

**schlecht**

``` sass
// Global Styles
.box
  position: relative
  margin-top: 20px
  color: $accent-color

// Media Queries
+respond-to(large)
  .box
    border: 1px solid $border-color

+respond-to(medium)
  .box
    margin-top: 10px
```

**gut**

``` sass
// Global Styles
.box
  position: relative
  color: $accent-color

// Media Queries
+respond-to(large)
  .box
    margin-top: 20px
    border: 1px solid $border-color

+respond-to(medium)
  .box
    margin-top: 10px

+respond-to(small)
  .box
    margin-top: 5px
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
```

## Linting

Zum Testen unseres Sass-Codes benutzen wir [Stylelint](https://stylelint.io). Die Dokumention der Regeln findet ihr [hier](https://stylelint.io/user-guide/rules/list). Unsere Konfiguration findet man in unserem [Template-Repo](https://github.com/zweitag/rails-project-template/blob/master/.stylelintrc).

### Verwendungsrichtlinien

Die Verwendung des Linters für neue Projekte ist **Pflicht**, damit Styleguide-Fragen in dem Projekt gar kein Thema werden.

* Dafür sollte jeder Entwickelnde lokal [die Editor-Integration](https://stylelint.io/user-guide/integrations/editor) verwenden, damit Fehler frühestmöglich erkannt werden.
* Falls die Editor-Integration keine Option sein sollte, bliebe auch die Möglichkeit eines Pre-Commit-Hooks.
* Als zusätzliches Sicherheitsnetz sollte ein Check auf Pull-Request-Ebene existieren. Hierfür stellen wir eine TravisCI-Integration zur Verfügung.

### Installation

1. `npm install -g stylelint stylelint-order`
2. Manueller Check: `stylelint --config .stylelintrc --formatter verbose '**/*.s+(a|c)ss'`
3. [Editor-Integration](https://stylelint.io/user-guide/integrations/editor) installieren

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
