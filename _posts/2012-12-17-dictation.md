---
title: Swift Dictation.app
description: The missing frontend for OS X Dictation
tags: [Swift Dictation, OS X, Multilingual, Languages]
image: /assets/img/2012/12/17/dictation/icon.png
---

![Swift Dictation]({{ page.image }})

OS X 10.8 comes with Dictation.  However, it does not work well in a multilingual environment because it can only set one language.  Then OS X 10.9 comes out with the support of multiple dictation languages, but you still cannot switch between different languages on keyboard easily.  In iOS, the dictation language is always synchronized with your keyboard language.  So, why don't we bring the iOS feature to OS X?  Here is the solution, a status bar application called `Swift Dictation`.

# Dictation Language goes with Input Method Language

![Input Method <=> Dictation](/assets/img/2012/12/17/dictation/input-method-and-dictation-language@2x.png){: srcset="/assets/img/2012/12/17/dictation/input-method-and-dictation-language@2x.png 2x" }

It is simple.  The small application just watches for changes of input methods then switches the dictation languages **on the fly**.

# User Guide

1. [Enable OS X Dictaton](https://support.apple.com/kb/HT5449) in System Preferences.
2. Install this application and make it open at login.
3. Set the accents that you prefer.
4. Done! The dictation language is going with your input method now.

---

[Checkout the source](https://github.com/ntkme/Swift-Dictation)
