# Sass Styleguide

## Inhaltsverzeichnis

- [Einleitung](#einleitung)
- [Manifest-Datei](#manifest-datei)
- [Style-Definitionen](#style-definitionen)
- [Syntax und Formatierung](#syntax-und-formatierung)
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
  &.highlighted
    background-color: $color-background-loud

.main-navigation--link
  display: block
  padding: 5px 0
  .main-navigation--item.highlighted &
    color: $color-text-inverted
```

## Syntax und Formatierung

Die Basis eines gemeinsam gelebten Stils ist ein grundlegendes Set an Syntax- und Formatierungsregeln. Eine strikte Einhaltung bringt Effizienzpotenziale, weil man Code schneller lesen, verstehen und abgleichen kann. Es ist weniger wahrscheinlich, dass man Dinge übersieht oder missversteht und niemand verbringt Zeit damit, seinen Arbeitsbereich durch Umformatierungen »für sich einzurichten«.

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

Zum Testen unseres Sass-Codes benutzen wir [Sass Lint](https://github.com/sasstools/sass-lint). Die Dokumention der Regeln findet ihr [hier](https://github.com/sasstools/sass-lint/tree/master/docs/rules). Unsere Konfiguration findet man in unserem [Template-Repo](https://github.com/zweitag/rails-project-template/blob/master/.sass-lint.yml).

### Verwendungsrichtlinien

Die Verwendung des Linters für neue Projekte ist **Pflicht**, damit Styleguide-Fragen in dem Projekt gar kein Thema werden.

* Dafür sollte jeder Entwickelnde lokal [die Editor-Integration](https://github.com/sasstools/sass-lint#ide-integration) verwenden, damit Fehler frühestmöglich erkannt werden.
  * Falls die Editor-Integration keine Option sein sollte, bliebe auch die Möglichkeit eines Pre-Commit-Hooks.
* Als zusätzliches Sicherheitsnetz sollte ein Check auf Pull-Request-Ebene existieren.
  *(Hierfür testen wir gerade die TravisCI-Integration.)*

### Installation

1. `npm install -g sass-lint`
2. Manueller Check: `sass-lint -config .sass-lint.yml '**/*.sass' --verbose --no-exit`
3. [Editor-Integration](https://github.com/sasstools/sass-lint#ide-integration) installieren

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
