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
        line-height: 1.2
      .fact-list__entry
        list-style-type: disc
      .fact-list__entry--highlighted
        font-weight: bold
      </pre>
    </td>
    <td>
      <pre lang="sass">
      .fact-list
        line-height: 1.2
      .fact-list--entry
        list-style-type: disc
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

#### assets/stylesheets/generic-components/_headline.sass
```sass
.headline
  +serif-font
  &.xl
    font-size: 32px
 ```

#### assets/stylesheets/generic-components/_media-list.sass
```sass
.media-list
  margin-bottom: 20px
  
.media-list--item
  list-style-type: none
  
.media-list--image
  display: block
 ```
 
 #### assets/stylesheets/specific-components/_product.sass
```sass
.product
  background-color: $color-product-background
  
.product--images
  +column(4)
  
.product--information
  +column(8)
 ```
