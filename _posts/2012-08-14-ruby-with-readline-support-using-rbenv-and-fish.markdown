---
layout: post
title: Ruby with Readline Support Using Rbenv and Fish
tags: [fish, readline, rbenv, ruby]
---

After moving from [rvm][] to [rbenv][] I re-installed ruby, everything was running smoothly until I had to debug (using pry). I tried to use common shortcuts like ↑, ↓ to browse the history and shortcuts like ^a, ^u to deal with inputted text but they were not working.

The reason why these functions/commands were not working was because my ruby was not installed with readline support.

> "The GNU Readline library provides a set of functions for use by applications that allow users to edit command lines as they are typed in. (…) The Readline library includes additional functions to maintain a list of previously-entered command lines, to recall and perhaps reedit those lines, and perform csh-like history expansion on previous commands."

<br/>

To solve this problem, I could have either installed [ruby readline][] or re-installed ruby with readline support, I opted for the latter.

Removing the folder where ruby was installed is all that's necessary to uninstall a ruby installed with rbenv:

{% highlight bash %}
➜  rm -rf (rbenv prefix 1.9.3-p194)
{% endhighlight %}

And then, to re-install it using the readline library installed with [brew][], I ran the following command:

{% highlight bash %}
➜  set -x CONFIGURE_OPTS --with-readline-dir=(brew --prefix readline); rbenv install 1.9.3-p194
{% endhighlight %}


[rvm]: https://rvm.io
[rbenv]: http://rbenv.org/
[brew]: http://mxcl.github.com/homebrew/
[ruby readline]: http://bogojoker.com/readline/
