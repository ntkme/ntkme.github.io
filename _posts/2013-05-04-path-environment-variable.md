---
title: $PATH
description: Remove a path in the elegant way
tags: [PATH, Environment Variable, Unix, Linux, BSD, OS X]
---

On POSIX and Unix-like operating systems, the $PATH is specified as a list of directories separated by `:`.  It's easy to change the $PATH by hardcoding.  However, sometimes it is needed to modify the $PATH programmatically and the most difficult part is removing a path from the $PATH elegantly.  The definition of elegant here is using a single line command that is human readable, portable, short and fast.  The common idea is using the $IFS, but the $IFS is ugly.  Instead of that, I am going to use `sed`.

# The Idea

> I got this idea from jQuery's [.removeClass()](https://github.com/jquery/jquery/blob/master/src/attributes/classes.js).  Thank you, jQuery.

Assuming we need to remove `/usr/local/bin` from the original $PATH shown as below.

``` sh
/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin
```

First, prepend and append `:` to the $PATH.

``` sh
:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:
```

Then, replace `:/usr/local/bin:` with `:`.

``` sh
:/usr/bin:/bin:/usr/sbin:/sbin:
```

Now, remove the prepended and appended `:`.

``` sh
/usr/bin:/bin:/usr/sbin:/sbin
```

Done!

# The Code

``` sh
PATH=`echo ":${PATH}:" | sed -e "s:\:/usr/local/bin\::\::g" -e "s/^://" -e "s/:$//"`
```

> `/usr/local/bin` can be replaced with any path or variable containing path.

## How it works

1. The `echo` command prepends and appends `:` to the $PATH.
2. The `|` pipes the output of `echo` to `sed`.
3. The first `sed` command removes the path.  In this command, `:` is used as a delimiter, because `:` is the only character reserved in the $PATH.  As a result, the actual `:` in the patterns are escaped as `\:`.  Although it has some impact on readability, the path to remove will never need to be escaped any more, which means you can use command like `sed -e "s:\:${path_to_remove}\::\::g"` without worrying about escaping.
4. The second and the third `sed` command removes the prepended and appended `:`.

---

# The bottom line

If you are using [Homebrew](https://brew.sh/), you may like to add this to your shell startup files.

``` sh
test -x /usr/local/bin/brew && export PATH=/usr/local/bin:`echo ":${PATH}:" | sed -e "s:\:/usr/local/bin\::\::g" -e "s/^://" -e "s/:$//"`
```
