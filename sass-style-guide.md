## Where to place code?

### Order of rules within a Sass file

The order of style rules that should be applied depends on the type of stylesheet.

#### Content-driven style definitions

* The easiest way to read and find specific content-driven style rules is to visually follow the order of the content on the rendered page. Iterate in this order:
  1. *Outside in*: Following ZASAF, you are likely to start with the page wrapper element.
  2. *Left-to-right*: Next, you start with the left-most element, followed by elements in the same (grid) row.
  3. *Top-to-bottom*: You go on top-to-bottom just how you would read that page
* Some things are shared, like a variable definition that is referred in multiple parts of your stylesheet. Just put it on top of the first block that uses it. A situation that you should avoid is sharing variables across files. If you cannot avoid that, put it on top of the file.
* Nestings within the namespace of an element start with pseudo classes, followed by pseudo elements, followed by modifiers, followed by direct children (`>`), followed by children further down the DOM tree (if even necessary). Example:

  ``` sass
  .main-navigation--item
    &:hover
      border-color: $color-border-loud
    &::before
      content: '*'
    &.active
      > .link
        +text-navigation-active
    > .link
      display: block
      padding: 5px 0
    .back-button
      color: $color-button-unobtrusive
  ```

#### Component files, manifest and base files

You should order your rules from generic to specific. Example:

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
@import utilities/hide-text
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
@import abstract-components/section-box

// Generic Component Imports
//--------------------------
@import generic-components/button
@import generic-components/skip-links

// Specific Component Imports
//---------------------------
@import specific-components/page/page-footer
@import specific-components/page/page-header
```

#### Order of property value assignments within a rule

If your setup is build on PostCSS, try [PostCSS Sorting](https://github.com/hudochenkov/postcss-sorting) in cunjunction with [SugarSS](https://github.com/postcss/sugarss).

1. Display:
  * visibility
  * opacity
  * display
2. Layout:
  * flex, flex-direction, flex-wrap, align-items, justify-content …
  * vertical-align
  * position
  * z-index
  * top, right, bottom, left
  * float
  * clear
  * overflow
3. Box-Model (box-sizing: border-box):
  * width
  * height
  * margin
  * border
  * border-radius
  * padding
  * outline
  * transform
  * box-shadow
4. Typography:
  * font
  * line-height
  * text-align
  * text-decoration
  * white-space
5. Colors:
  * color
  * fill
  * background
6. Miscellaneous:
  * cursor
  * transition
  * pointer-events
  * …

## Syntax conventions

* We use the old **Sass indented** syntax because it is the more rigid syntax of **Sass** and facilitates code homogeneity. The best way to end the fight between different parenthesis placement practictioners is to simply remove parenthesis. ;)
* Be consistant with indentation – we’re using two spaces.
* Use english for naming things. The English language has proven itself among coders as the standard.
* Use lowercase for class names.
* Multi-word selectors are **separated by hyphens (`-`)**. This is consistent with general CSS conventions including CSS attribute names and Sass functions.
* Please place **empty lines only between blocks of code that are not nested**. If you are working on a part of the code you already know your way around. But it is important that you get a quick overview if the whole file is not familiar to you. Besides, it’s easier to recognize the nesting structure if nestings are in one block with the parent selector.
* Be consistent in declaration order, cluster related properties (see above).
* Be consistant with quotes – we’re using single quotes.
* Quote attribute values in selectors, e.g. `input[type='checkbox']`.
* Write one selector per line, one rule per line.
* Put a space after `:` in property declarations, e.g. `margin: 0`
* Include a space after each comma in comma-separated property or function values, e.g. `rgba(255, 255, 255, 0)`.
* Use rgb and rgba color codes, because most of us can envision what color results by mixing red, green and blue.

It could be a quick-win to use [Sass Lint](https://github.com/sasstools/sass-lint) in your projects.

## Coding conventions

### Avoid dangerous selectors

If a selector is too generic, it’s dangerous. In 99% of cases you have to overwrite this rule somewhere. Be more specific. Try using a class instead. (Exception: CSS-Resetstyles)

**bad**

``` sass
header
  padding: 10px 20px

h2
  margin: 0

ul
  list-style-type: circle
```

**good**

``` sass
.page-header
  padding: 10px 20px

.subtitle
  margin: 0

.link-list
  list-style-type: circle
```

### Avoid element selectors

Element selectors are expensive. Like the rule above, be more specific. Try using a class instead. Furthermore elements like `<div />` and `<span />` should always have a class-attribute in your markup because otherwise they have no function anyway and you can waive them.

**bad**

``` sass
.navigation div
.navigation span
.navigation ul
```

**good**

``` sass
.navigation--box
.navigation--title
.navigation--list
```

### Avoid qualifying class names with element selectors

It’s counterproductive because you unnecessary heighten the specifity.

**bad**

``` sass
ul.link-list
div.example
a.back
```

**good**

``` sass
.link-list
.example
.back
```

### Avoid the descendant selector

The descendant selector is the most expensive selector in CSS. You should target directly if possible.

**bad**

``` sass
.link-list li a
```

**good**

``` sass
.link-list--link
```

### Avoid deep nesting

Following to the rule above you should also try to nest your selectors maximum 3 levels deep (pseudo-elements and pseudo-classes not counting).

**bad**

``` sass
.link-list
  li
    a
      span
        &:before
          content: 'i'
```

**good**

``` sass
.link-list--info
  &::before
    content: 'i'
```

### Avoid using the same selector for styling and JS

Separation of concerns. If you later change the class name in your CSS you don’t break your JS component.

**bad**

``` sass
.dialog-opener
```

``` js
$('.dialog-opener')
```

**good**

use a data-attribute:

``` js
$('[data-dialog-opener]')
```

or use an ID:

``` js
$('#dialog-opener')
```

### Use shorthand properties where possible

It’s shorter and easier to read.

**bad**

``` sass
.box
  padding-top: 0
  padding-right: 10px
  padding-bottom: 20px
  padding-left: 10px
```

**good**

``` sass
.box
  padding: 0 10px 20px
```

### Declare only those values you really want to set

If you set too much values it might be possible that you accidentally overwrite an inherited one. Or the other way round you later have to overwrite this value once again because the newly set value inherits itself.

**bad**

``` sass
.box
  padding: 0 10px 0 0
  background: rgba(0, 0, 0, 0.5)
```

**good**

``` sass
.box
  padding-right: 10px
  background-color: rgba(0, 0, 0, 0.5)
```

### Omit unit specification after “0” values

Zero is zero. :)

**bad**

``` sass
.box
  margin: 0px
```

**good**

``` sass
.box
  margin: 0
```

**exception, where you don’t omit**

``` sass
.box
  transform: rotate(0deg)
```

### Use number keywords 100–900 for font-weight

It’s the typographic standard to use number keywords. Also it’s shorter and saves some bits.

**bad**

``` sass
.box
  font-weight: bold
```

**good**

``` sass
.box
  font-weight: 700
```

## Usage of comments

We want to make it easy for others to find their way through stylesheets. Therefore, we group rules and give them a title by writing a comment. We distinguish 3 types of comments:

* Simple Comments, refering only to the directly following ruleset
  * Syntax: begins with `//`
* Group Comments that group multiple rulesets together
  * Syntax: separated with `---` from the first ruleset of the group
* Headline Comments that group multiple groups together
  * Before you introduce this hierarchy level, consider to split up your stylesheet into multiple partials
  * Syntax: separated with `===` and a new line from the first group comment

Example:

``` sass
// Photo Styles
//=============

// Shared Photo Styles
//--------------------
// The photo-corners mixin provides cross-browser … (simple comment)
=photo-corners
  …

.photo-gallery
  …

.photo-stream
  …

// Photo Contest Panel
//--------------------
.photo-contest-panel
  …

// Video Styles
//=============

// Video Gallery
//--------------
.video-gallery
  …

// Video Contest Panel
//--------------------
.video-contest-panel
  …

```
