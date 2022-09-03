---
title: Markdown Style
description: Preview of rendered html elements
tags: [markdown, css, stylesheet, Responsive Web Design]
image: /assets/img/2012/08/01/markdown-style/markdown-style@2x.png
---

Inspired by [iA Writer](https://ia.net/writer/), a quintessential writing machine, the markdown style is rendered by [html-md.css](https://github.com/ntkme/html-md.css).  Purely implemented in CSS, it is optimized for all screen sizes.  To see the responsive effect, please try changing the viewpoint width or height.

# Paragraph

ALICE was beginning to get very tired of sitting by her sister on the bank and of having nothing to do: once or twice she had peeped into the book her sister was reading, but it had no pictures or conversations in it, “and what is the use of a book,” thought Alice, “without pictures or conversations?”

So she was considering, in her own mind (as well as she could, for the hot day made her feel very sleepy and stupid), whether the pleasure of making a daisy-chain would be worth the trouble of getting up and picking the daisies, when suddenly a White Rabbit with pink eyes ran close by her.

---

# Heading 1

## Heading 2

### Heading 3

#### Heading 4

##### Heading 5

###### Heading 6

---

# Blockquote

> Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus. Praesent commodo cursus magna, vel scelerisque nisl consectetur et. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus. Donec ullamcorper nulla non metus auctor fringilla.

> > Nulla vitae elit libero, a pharetra augue. Donec sed odio dui. Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Cras mattis consectetur purus sit amet fermentum. Curabitur blandit tempus porttitor. Curabitur blandit tempus porttitor.

---

# List

- List item with a much longer description or more content.
- List item
- List item
    * Nested List Item
    * Nested List Item
    * Nested List Item
- List item
- List item
- List item

---

# Number List

1. List item with a much longer description or more content.
2. List item
3. List item
    1. Nested List Item
    2. Nested List Item
    3. Nested List Item
4. List item
5. List item
6. List item
7. List item
8. List item
9. List item
10. List item
11. List item

---

# Code Block

<pre><code>I am a code block.

  へ　へ
  の　の
  　も　
  　へ　
</code></pre>

---

# Horizontal rules

---

# Link

[{{ site.title }}]({% link index.md %})

---

# Emphasis

*single asterisks*

_single underscores_

**double asterisks**

__double underscores__

<del>double tilde</del>

---

# Inline code

Use the `printf()` function.

``There is a literal backtick (`) here.``

---

# Image

![Markdown Style]({{ page.image }}){: srcset="{{ page.image }} 2x" }

---

# Highlighter

``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="format-detection" content="telephone=no">
  <title>html-md.css</title>
  <link rel="stylesheet" href="https://unpkg.com/html-md.css@3.0.0/dist/html-md.css" integrity="sha384-DJQnz+pdiayvnbp0Idbo9qIYroVfiDasEHtlD8rSbNclbGUREU8ju/YQ1NMq7Fdp" crossorigin="anonymous">
</head>
<body>
  <div class="html-md"></div>
</body>
</html>
```
