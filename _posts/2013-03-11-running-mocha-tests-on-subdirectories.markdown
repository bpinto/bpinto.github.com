---
layout: post
title: Running mocha tests on subdirectories
tags: [mocha, node.js, test]
---

Over the weekend I started a [node.js][] app and decided to give [mocha][] a try. Despite all google I did and all github issues I read, I couldn't find an easy way to run tests that are on subdirectories.

But why should it be **easy**? :P

Mocha advices you to use Makefiles to setup which tests it should run, IMHO this is over-engineering – maybe one day you will need it.
The solution I propose is quite easy, and I just needed to look at the [mocha binary file][] and see the possible arguments it accepts.

{% highlight javascript %}
.option('-r, --require <name>', 'require the given module')
.option('-R, --reporter <name>', 'specify the reporter to use', 'dot')
.option('-u, --ui <name>', 'specify user-interface (bdd|tdd|exports)', 'bdd')
.option('-g, --grep <pattern>', 'only run tests matching <pattern>')
.option('-i, --invert', 'inverts --grep matches')
.option('-t, --timeout <ms>', 'set test-case timeout in milliseconds [2000]')
.option('-s, --slow <ms>', '"slow" test threshold in milliseconds [75]')
.option('-w, --watch', 'watch files for changes')
.option('-c, --colors', 'force enabling of colors')
.option('-C, --no-colors', 'force disabling of colors')
.option('-G, --growl', 'enable growl notification support')
.option('-d, --debug', "enable node's debugger, synonym for node --debug")
.option('-b, --bail', "bail after first test failure")
.option('-A, --async-only', "force all tests to take a callback (async)")
.option('--recursive', 'include sub directories')
.option('--debug-brk', "enable node's debugger breaking on the first line")
.option('--globals <names>', 'allow the given comma-delimited global [names]', list, [])
.option('--ignore-leaks', 'ignore global variable leaks')
.option('--interfaces', 'display available interfaces')
.option('--reporters', 'display available reporters')
.option('--compilers <ext>:<module>,...', 'use the given module(s) to compile files', list, [])
{% endhighlight %}

The ```--recursive``` option does what we need. So, one just needs to run mocha using this option and mocha will find all files under the test directory.

{% highlight bash %}
➜ mocha --recursive
{% endhighlight %}

In order to set this as a default argument, configure this on your ```test/mocha.opts``` file.

{% highlight bash %}
➜  cat test/mocha.opts
--recursive
{% endhighlight %}

This also works when running ```npm test```, as long as your ```package.json``` is configured properly:

{% highlight javascript %}
"scripts": {
  "test": "mocha"
}
{% endhighlight %}

[node.js]: http://nodejs.org/
[mocha]: http://visionmedia.github.com/mocha/
[mocha binary file]: https://github.com/visionmedia/mocha/blob/master/bin/_mocha
