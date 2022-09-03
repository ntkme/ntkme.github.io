---
title: github:buttons
description: The buttons for developers
tags: [GitHub, Button, Follow, Star, Fork, Issue, Gist, Octicon, API]
image: /assets/img/2014/01/02/github-buttons/github-buttons@2x.png
---

# Introducing the Unofficial github:buttons

![github:buttons]({{ page.image }}){: srcset="{{ page.image }} 2x" }

Made by developer and for developers, [github:buttons](https://buttons.github.io/) used a very different approach comparing to other github buttons service.  It was designed with the flexibility to customize almost everything including link, text, icon, and count.  Unlike the widely used [mdo/github-buttons](https://github.com/mdo/github-buttons), in this **pixel perfect** [ntkme/github-buttons](https://github.com/ntkme/github-buttons), you will never worry about iframe sizing and overflowing.

**[Get started!](https://buttons.github.io/)**

---

# The Making of the github:buttons

## \<iframe\>

Twitter, Facebook, Google+ ...  All buttons services from those big vendors are based on iframe.  Why?  Because iframe is protected by [same-origin policy](https://developer.mozilla.org/docs/Web/Security/Same-origin_policy), which means it won't be affected by stylesheets or scripts in parent window.

### It's all about the same-origin policy

Saying that your website is hosted on domain A, and the buttons are hosted on the domain B, what would happen to the button iframe?  Well, first of all, if the button iframe on domain B is embedded directly, there is no way to get its content size because the access to its content is blocked by same-origin policy.  That's exactly the problem [mdo/github-buttons](https://github.com/mdo/github-buttons) faces.  What about render the button in an empty iframe?  Not a bad idea.  In that case, since the iframe does not have the src attribute, it's considered to be in the same-origin as domain A.  However, web fonts from domain B will be blocked in the iframe on domain A unless the [Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) on domain B is properly configured.

So, the answer is combining the two cases here.

1. Create an empty iframe as a sandbox in domain A and render the button.
2. Calculate the rendered button size.
3. Open a new iframe in domain B and render the button again.
4. Set the new iframe size to fit the previously calculated button size.

#### Pixel Perfect Content Size

The default size of an iframe is 300px by 150px, which can change the document flow significantly.  So, it comes the idea of hiding an iframe before it is resized properly.  `visibility: hidden;` simply does not work because it still takes up space.  `display: none;` is not a choice because IE 11 does not render it at all.  The only way to do it correctly is setting `width: 1px; height: 0;`.  With that, calling `body.scrollWidth()` and `body.scrollHeight()` should return the content size of an iframe.

That is not the end of the story.  `body.scrollWidth()` returns an integer rounded from the actual float value.  On an @2x display, the size can be off by 1/2px.  In the case that the number is rounded down, the iframe will be cut off.  The solution is to use `body.getBoundingClientRect()`, which returns the float, and ceil it to the closest physical pixel.

However, that is still not the end of the story.  WebKit rounds down iframe size to closest 1/2px on @3x display, which means that in the worst case the iframe will be cut off by 1/3px.  Thus, the ultimate solution is to round the float to the closest physical pixel, then ceil the value to closest 1/2px, which is illustrated by the following formula.

``` javascript
Math.ceil(Math.round(px * devicePixelRatio) / devicePixelRatio * 2) / 2 || 0
```

#### iframe.onload

iframe onload event is not reliable because some browsers implemented the onload event in a wrong way, especially when there are DOM changes during the page loading.  In my tests, IE 9 and Android Browser 2.x fire the onload event before the dynamically injected sciprts complete execution.  Presto-based Opera has a even more interesting bug.  With memory cache turned on, it will never fullfill a javascript request in iframe that is dynamically injected before DOM ready, after the file is cached in memory.  So, a polyfill for the iframe.onload event is required for those browsers.

---

## \<script async defer\>\</script\>

**Performance** does matter.

So both async and defer attribute are used on the script element.  They ensure that the script will never slow down the DOM loading.  Also, to reduce requests, the scripts used in parent window and iframe are combined into one script, which lets the browser read it from cache.

---

## Octicons

GitHub's iconic web font is pretty awesome, but it brings a little bit trouble.  Most of the major browsers request a web font only after they found it's used somewhere in the web page, and they load the web fonts _asynchronously_.  Sometimes it happens that the `window.onload` event is fired earlier than the web fonts are rendered, which leads to the incorrect iframe size.

In addition, there is no native `load` event for web font.  The [typekit/webfontloader](https://github.com/typekit/webfontloader) may be a solution, but it still load the web font _asynchronously_, so it doesn't help much.  The web font loader also increases the requests and slows down the whole process in this situation.

### Pre-rendering Octicons

The only reliable choice left is pre-rendering, because it doesn't depend on the web font load event.  I calculated all the icon sizes in em unit, then generated a stylesheet containing their sizes.

### \<!--[if lte IE 8]--\> Octicons \<![endif]--\>

This project **do** support the old IE.  It sounds crazy, isn't it?  Actually it is not too difficult since most of the incompatibilities are came from the octicons.

IE 6 and 7 don't support css pseudo-elements `:before` and `:after`, so I use a css expression hack like this one.

``` css
.octicon-mark-github { zoom: expression( this.innerHTML = '&#xf00a;' ); }
```

IE 8 is worse than IE 6 and 7.  Although IE 8 supports `:before` used in octicons, it still *randomly* uses the local font instead of the octicons.  [This related question on Stack Overflow](https://stackoverflow.com/questions/9809351) gives a solution that forcing IE 8 to redraw octicons.

---

# Still want to know more?

Take a look at [ntkme/github-buttons](https://github.com/ntkme/github-buttons)!
