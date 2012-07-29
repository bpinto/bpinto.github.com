---
layout: post
title: Fish and RVM
tags: [fish, oh my fish, rvm]
---

Decided to move from bash/zsh/etc to [fish][] shell? Developing in ruby? So, you probably use rvm.

If you are _lucky_, you found this [step-by-step tutorial][rvm and fish] that integrates rvm with fish. After using fish for a while, you noticed that your **cd** function is missing some features... WTF!?

Why would the cd function lose features if _"RVM just manipulates environment variables"_?  Rvm is not **just** manipulating environment variables, it also overrides shell commands like cd.

<br/>
The steps described on the tutorial are:

``
  set -l GITHUB https://raw.github.com/lunks/fish-nuggets/master/functions
  curl --create-dirs -o ~/.config/fish/functions/rvm.fish $GITHUB/rvm.fish
  curl -o ~/.config/fish/functions/cd.fish $GITHUB/cd.fish
``

As you can see, the third step is actually redefining the cd function.
<br/>

Fish has a philosophy different than most of the shells: _"Many other shells have a large library of builtin commands. Most of these commands are also available as standalone commands, but have been implemented in the shell anyway for whatever reason. To avoid code duplication, and to avoid the confusion of subtly differing versions of the same command, fish only implements builtins for actions which cannot be performed by a regular command."_

While you have the builtin command 'cd' you also have the regular command defined in fish. The latter adds more features to the 'cd' function.

Functions in fish are lazy loaded, and ones priority depends on the directory that it was defined. When following the step-by-step guide, the cd function is copied to a user specific directory which has a higher priority than the default fish functions directory. Thus, the regular cd command is never loaded.


Currently, fish has no support for replacing functions without losing its functionality, and as I **was** using rvm at that time I decided to create such behaviour.

<br/>
One of the features your cd command loses is being able to cd into your previous path:

``
➜  ~ cd -
cd: The directory '-' does not exist
``

The simplest solution to this problem is using [Oh My Fish][]'s rvm plugin and another solution is copying the code from fish function and adding it to the rvm cd function (is this even a solution? I wonder).

Anyway, I would like that any user of fish could have the simple solution, so I've just sent an e-mail to fish's maintainer asking him to look at the code before I ask for a pull request.

[Fish]: http://ridiculousfish.com/shell/
[Oh My Fish]: https://github.com/bpinto/oh-my-fish/
[RVM and fish]: https://rvm.io/integration/fish/
