---
title: Zsh Prompt
description: Beautiful and Informative
tags: [zsh, zsh prompt]
image: /assets/img/2012/10/06/zsh-prompt/zsh-prompt@2x.png
---

![Zsh Prompt]({{ page.image }}){: srcset="{{ page.image }} 2x" }

Few days ago, I saw Steve Losh's [My Extravagant Zsh Prompt](https://stevelosh.com/blog/2010/02/my-extravagant-zsh-prompt/).  It was fantastic.  My fork has the very same basis, and I just use my approach to make it better.

---

# PROMPT

## Current Working Directory

- `/` is highlighted with a different color to make the path easy to read.
- Parent directory names are truncated in favor of zsh path completion.

## Git Status

It shows current branch or HEAD for detached state.

- `!` means the repository is dirty.
- `?` means untracked files exist.

## PROMPT Character

The PROMPT Character shows in following order:

- `±` for [Git](https://git-scm.com/)
- `√` for root
- `↳`

---

# RPROMPT

## Battery Indicator

It indicates not only the remaining capacity but also the status (charging, discharging or full).  It is [implemented in bash](https://github.com/ntkme/home/blob/master/.config/bash/functions/battery-indicator) and compatible with both OS X and Linux.

---

# Floating PROMPT

Instead of staying on the right side of the input line, the right prompt is floating on the top right.  If you understand CSS, the concept is just like the following code, except that the actual implementation in zsh is [way more complex](https://github.com/ntkme/home/blob/master/.config/zsh/functions/zsh_prompt_float).

``` css
prompt {
  float: left;
}

rprompt {
  float: right;
}
```

---

Checkout my [dotfiles](https://github.com/ntkme/home)

