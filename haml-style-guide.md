## HAML best practices

Because of the importance indentation has on how your code is rendered, the indents should be consistent throughout the document. Any differences in indentation will throw an error. It's common-practice to use two spaces.

### How to use HTML elements

To write your tags, use the percent sign followed by the name of the tag

``` haml
%body
  %header
    %nav
```

The `<div>` tag is the default element, so if you put a class on it, it can be written like this:

``` haml
.container
```

instead of:

``` haml
%div.container
```

If you want to add multiple classes, simply chain them:

``` haml
.container.large.unobtrusive
```

### How to use comments

There’re three different styles to write comments in Haml.

**Single-line comment:**

``` haml
/ This is what a comment looks like in Haml.
```

**Multi-line comment:**

``` haml
/
  To write a multi line comment, indent your commented code to be
  wrapped by the forward slash
```

**Silent comment:**

``` haml
-# This is a silent comment, which means it won’t be rendered into the document at all.
```

**Note:** Single and multiline comments will be redenred into the document. Therefore we commonly choose the third option because usually comments are meant for developers only.

### Use blocks

HAML isn’t Ruby, but it still incorporates some convenient elements of it, and there’s no better example of that than the block syntax.

#### Morph code into block format with `precede`, `succeed` and `surround`

**Example:**

**Note**: The first word of this sentence is bold and is immediately followed by an unbold colon, without any whitespace in between the colon and the bold text. In HAML, it’s surprisingly *unobvious* how to pull off this seemingly simple task.

HAML provides helpers to get around this:

``` haml
= precede '*' do
  = field_name
```

``` haml
= succeed ':' do
  %strong Note
```

``` haml
= surround '(', ')' do
  %em Optional
```

**Bad practice:**

Using inline HTML and `content_tag`

``` haml
= content_tag(:strong, 'Note') + ':'
```

This isn’t terrible, but the above blocks are much more readable!

#### Use `link_to` as a block

When wrapping long or multiple elements in a link tag, It’s wise to use `link_to` in block format.

``` haml
= link_to email_form_path, class: ['button'] do
  = image_tag 'mail-icon.png'
  Email us
```

**Bad practice:**

Long link_to calls

``` haml
= link_to image_tag('mail-icon.png') + ' Email us', email_form_path, class: ['button']
```

Rails routing is best used with its built-in helper methods, where objects can be used as routes. Additionally, these helper methods were also made to be orthogonal from raw HTML.

**Example:**

``` haml
= link_to 'View User Profile', @user
```

**Bad practice:**

``` haml
%a{href: users_path(@user)} View User Profile
```

#### Use `list_of`

A lesser known – and thus, lesser used – feature of HAML is `list_of`.
This method generates * elements for you as you iterate over a list of items.

**Example:**

``` haml
%ol
  = list_of @courses do |course|
    %h3= link_to course.name, course
    %ul
      = list_of course.students do |student|
        = _tag student.gravatar_url
        = link_to student.name, student
```

**Bad Practice:**

Whitespace abuse. Every time you use `list_of`, you’ve saved one indentation. This is especially useful when you’ve got nested lists. If you didn’t use it, you’d see something like this instead:

``` haml
%ol
  - for course in @courses
  %li
    %h3= link_to course.name, course
    %ul
      - for student in course.students
      %li
        = image_tag student.gravatar_url
        = link_to student.name, [course, student]
```

### Use filters

Use the colon to define [Haml filters](http://haml.info/docs/yardoc/file.REFERENCE.html#filters). This allows you to pass an indented block of text as input to another filtering program and add the result to the output of Haml.

**Example:**

``` haml
:javascript
  console.log('This is inline script');
```

**Bad practice:**

``` haml
%script(type="text/javascript")
  console.log('This is inline script');
```

### Use curly braces

The name of the game here is readability and a big part of readability is DRY.

The growing popularity of HTML5 “data-” attributes means that you can expect to use them when you use tools like Bootstrap. HAML handles a “data” hash in a neat way that prevents you from having to keep writing (and maintaining!) these “data-” prefixes.

**Example:**

``` haml
.profile{data: {name: 'Popeye', favorite_food: 'Spinach'}} Loading
```

will be rendered to:

``` html
<div class="profile" data-name="Popeye" data-favorite-food="Spinach">Loading</div>
```

Note that you can also pass in hashes directly into the data attribute, and it will still be rendered as expected:

``` haml
.profile{data: @profile_data} Loading
```

**Bad practice:**

``` haml
.profile(data-name='Popeye' data-favorite-food='Spinach') Loading
```

### Use alternative expression methods

At this point, you know exactly what `-` and `=` do in your HAML views, but what about the other fun Ruby expression outputting methods? These alternative outputting methods have got to be some of the most underused features of HAML, yet is one of the most useful, especially for internal communication.

**Example:**

Say you want a line to be escaped, no matter what. A good example of this is user input. This is accomplished (and communicated well internally) with the `&=` method:

``` haml
&= @user.nickname
```

**Note:** if escape_html is set to true, `=` will function exactly the same as `&=`

On the same note, let’s say you output something that you definitely don’t want to escape. This can also be accomplished and communicated with:

``` haml
!= link_to 'View More →', about_path
```

Lastly, sometimes you have a long string that just has one small Ruby expression. In this case, you can use Ruby-style interpolated code directly into HAML:

``` haml
%h3 Last visit was #{time_ago_in_words @user.last_signed_in_at} ago
```

**Bad practice:**

If you’ve ever seen html_safe in a view or a helper, you can be certain that you have an excellent case to use `!=`:

``` haml
= link_to 'View More →'.html_safe, about_path
```

Additionally, we know that users can sometimes enter some untrustworthy text. Let other users of your codebase know with a `&=`, otherwise it will blend in with the other `=` expressions:

``` haml
= @user.job_title_from_user
```

**Another bad practice:**

When you don’t know about interpolated Ruby code, you’re bound to pull this one:

``` haml
%h3= "Last visit was #{time_ago_in_words @user.last_signed_in_at} ago"
```

Don’t bother with these strings – just cut right into the interpolated input expressed above and save characters and eye strain.

### HAML Cheat-Sheet

* https://www.cheatography.com/specialbrand/cheat-sheets/haml/
