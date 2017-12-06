# How do I customize components?

The ZASAF follows the Convention over Configuration principle: You should write components that come with good defaults.
If you want to deviate from the default there are two options:

1. Use or write a configuration option for the component using a modifier (see below)
2. Override or extend the component based on its location

Each of those options come with its own pros and cons, so choose carefully.
Translate your rule into a human-understandable sentence – is this what you want to declare?

## Example

``` haml
.product-page
  %aside
    .teaser
      …
```

``` sass
.teaser
  border: 1px solid $color-border-box
  padding: 10px
```

So we start with a basic `.teaser` component in the sidebar of the `.product-page`.
Now we look at the PSD or HTML or whatever the designer gave us and observe that this teaser should be more grayish than normal.

### Option 1: Add a modifier

``` sass
.teaser
  border: 1px solid $color-border-loud
  padding: 10px
  &.unobtrusive
    border-color: $color-border-silent
    color: $color-font-silent
```

We added the `.unobtrusive` modifier to the component, which we can now use in the markup:

``` haml
.product-page
  %aside
    .teaser.unobtrusive
      …
```

If you use several modifiers be sure to separate them by category. These categories must be mutally exclusive which means that you're not allowed to chain modifiers of the same category to avoid specificity issues.

Examples:

``` sass
.teaser
  // Function
  &.primary
  &.secondary
  // Size
  &.narrow
  &.wide
  // Obtrusiveness
  &.obtrusive
  &.unobtrusive
  …
```

### Option 2: Location-based customization

Now you are saying: "This particular teaser should be unobtrusive."

Is this accurate? Or would it be more accurate to say "All teasers on the product page sidebar should be unobtrusive."? No problem, you can also express that (option 2.a):

``` haml
.product-page
  %aside.product-page--sidebar
    .teaser
      …
```

Note that we removed the modifier from the single HTML element and introduce a proper name for the product page sidebar.

``` sass
.teaser
  border: 1px solid $color-border-loud
  padding: 10px

  .product-page--sidebar &
    border-color: $color-border-silent
    color: $color-font-silent
```

If we had used the selector `.product-page aside .teaser` in the stylesheet (option 2.b), we would have said: "All teasers in aside sections on the product page should be unobtrusive."

If this is even more accurate then go for it. But be aware of unwanted complexity. If you are sure that any developer who adds an aside section to the product page is happy that all teaser in it are unobtrusive, you're doing the right thing.

But if anyone has to override the component customization, they have to change your selector to something like `.product-page aside:not(.product-cross-sellings) .teaser` or even have to revert your unobtrusive stylings:

``` sass
.product-page
  %aside.product-cross-sellings
    .teaser
      border-color: $color-product-cross-sellings
      color: inherit
```

Such overrides are inefficient and hard to maintain so you want to avoid them.
A rule like "All teasers in aside sections should be unobtrusive" (without the page namespace) certainly goes too far.
