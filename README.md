# Zweitag Approach to Scalable and Adaptable Frontends (ZASAF)

[Einleitung zur Wichtigkeit von CSS Guidelines](http://cssguidelin.es/#introduction)

ZASAF folgt dem Grundgedanken von [BEM](http://getbem.com/) und [SMACSS](http://smacss.com), dass Frontends modular aufgebaut werden sollten.
Dabei unterscheiden wir jedoch noch einmal zwischen generischen und spezifischen Komponenten.
Wir organisieren unseren Stylesheet-Ordner wie folgt:

* generic-components
* specific-components
* globals
* utilities

## Komponenten

### Generische Komponenten

Als generisch ordnen wir die Komponenten ein, die ihrer Natur nach wiederverwendbar sind.

#### Beispiele

`.button`, `.data-table`, `.fact-list`, `horizontal-form`

#### Vorteile

* Konsistenz des UIs
* Effiziente Zusammenstellung neuer UIs durch Wiederverwendung
* Alle Instanzen einer Komponente können zentral angepasst werden

#### Eigenschaften des Namens

* beschreibt die Funktion, nicht den Inhalt
* ist i.d.R. nicht spezifisch für die Anwendung
* ist ein Nomen, ggf. mit Adjektiv davor

### Spezifische Komponenten

Frontend-Komponenten, die für einen bestimmen Anwendungsfall geschaffen werden und nicht zur allgemeinen Verwendung gedacht sind, kennzeichnen wir als spezifische Komponenten.

#### Beispiele

`.company-search`, `.product-page`, `.about-us-illustration`

#### Vorteile

* simpler zu schreiben, da nicht auf Wiederverwendung ausgelegt
* simpler zu warten, da es nur 1 Instanz der Komponente geben kann
* darf spezifische Details wie Layoutinformationen enthalten

#### Eigenschaften des Namens

* beschreibt eindeutig einen konkreten Inhalt
* ist i.d.R. spezifisch für die Anwendung
* ist ein Nomen, ggf. mit Adjektiv davor

### Modifier

Varianten und Status einer Komponente werden mit Modifier abgebildet. Sie treten immer nur in direkter Verbindung mit einer Komponente auf.

#### Beispiele

`.button.primary`, `.text-link.unobtrusive`, `.product-list--entry.sold-out`, `.button.primary.disabled`

#### Eigenschaften des Namens

* beschreibt einen Zustand oder besonderes Verhalten einer Komponenteninstanz, wodurch eine Variante entsteht
* ist idealerweise ein einfaches Adjektiv
* steht nie alleine, sondern ist nur in Verbindung mit der Komponente eindeutig

### Block, Element, Modifier (BEM)

Die Unterscheidung von generischen und spezifischen Komponenten ist der wichtigste Unterschied zu BEM.
Die anderen Konzepte verwenden wir mit der gleichen [Semantik wie BEM](http://getbem.com/introduction/).

* Block in BEM = generische oder spezifische Komponente in ZASAF
* Element eines Blocks in BEM = Element einer Komponente in ZASAF
* Modifier eines Blocks in BEM = Modifier einer Komponente in ZASAF

Die Syntax von BEM finden wir jedoch etwas sperrig und haben uns für ein anderes Namensschema entschieden:

<table>
  <tr>
    <th>BEM</th>
    <th>ZASAF</th>
  </tr>
  <tr>
    <td>
      <pre lang="haml">
      %ul.fact-list
        %li.fact-list__entry
        %li.fact-list__entry.fact-list__entry--highlighted
      </pre>
    </td>
    <td>
      <pre lang="haml">
      %ul.fact-list
        %li.fact-list--entry
        %li.fact-list--entry.highlighted
      </pre>
    </td>
  </tr>
  <tr>
    <td>
      <pre lang="sass">
      .fact-list
        list-style-type: disc
      .fact-list__entry
        list-style-type: discline-height: 1.2
      .fact-list__entry--highlighted
        font-weight: bold
      </pre>
    </td>
    <td>
      <pre lang="sass">
      .fact-list
        list-style-type: disc
      .fact-list--entry
        line-height: 1.2
        &.highlighted
          font-weight: bold
      </pre>
    </td>
  </tr>
</table>

* Der Komponenten-Namensraum wird also mit `--` vom Elementnamen abgegrenzt.
* Modifier kommen ohne Namensraum aus, da sie ausschließlich in Kombination mit einer Komponente bzw. einem Element eingesetzt werden

### Beispiel
#### views/products/show.html.haml
``` haml
.product
  %aside.product--images
    %ul.media-list
      %li.media-list--item
        %img.media-list--image{…}
      %li.media-list--item
        %img.media-list--image{…}
      
  .product--information
    %h1.headline.xl Musterprodukt
```

#### assets/stylesheets/generic-components/_headline.css.sass
```sass
.headline
  +font-serif
  &.xl
    +text-copy(xl)
 ```

#### assets/stylesheets/generic-components/_media-list.css.sass
```sass
.media-list
  margin-bottom: 20px
  
.media-list--item
  width: 100%
  
.media-list--image
  display: block
 ```
 
 #### assets/stylesheets/specific-components/_product.css.sass
```sass
.product
  background-color: $color-product-background
  
.product--images
  +column(4)
  
.product--information
  +column(8)
 ```

## Globales und Hilfsmittel
### Globales
Im Order `globals` finden sich globale Basisstile und Konfigurationen:

* _base.css.sass: Styling der HTML-Elemente (z.B. `strong {font-weight: 700}`)
* _colors.css.sass: Farbdeklarationen (z. B. `$color-accent: $curious-blue`)
* _reset.css.sass: Standard-Reset der HTML-Elemente (z.B. `ol, ul {list-style: none}`}
* _typography.css.sass: Font-Stack-Deklarationen und Text-Mixins (z.B. `@mixin font-serif {font-family: $font-stack-serif}`
* …

### Hilfsmittel
Im Ordner `utilitis` finden sich Mixins, die in Komponenten eingesetzt werden können:

* _clearfix.css.sass
* _font-smoothing.css.sass
* _grid.css.sass
* _responsify.css.sass
* …

## Selector Best Practices

* [Customizing Components](customizing-components.md)
* [Selector Intent](http://cssguidelin.es/#selector-intent)
* [Portability](http://cssguidelin.es/#portability)
* [Specificity](http://cssguidelin.es/#specificity)

## Style Guides
* [Haml Style Guide](haml-style-guide.md)
* [Sass Style Guide](sass-style-guide.md)
