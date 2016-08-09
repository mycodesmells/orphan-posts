# Introduction to SASS

Creating and maintaining a large code base can often be quite a challenging task. You need to keep yourself from code duplications, want to keep everything organized, have a possibility for easy refactoring. One of the hardest things to manage in a web project are styles, as CSS can hardly be defined as language. Fortunately, there are tools that can help you with it, and one of the most popular is SASS - a CSS preprocessor.

### Style Preprocessing

Preprocessing is an idea of having one syntax transformed into another using some kind of tool. While this may sound confusing, it has many important benefits, especially in the world of CSS. First of all, as the process is automated, you receive output styles having a certain high level of quality, and the _style_ of your styles is consistent across application. This as well might sound like an overkill, as you can just keep attention to what you're doing (which is impossible in larger projects, let's be honest). But preprocessing bring a few cool features that otherwise would not be available using plain CSS.

There are a few options when it comes to CSS preprocessing, with [Stylus](http://stylus-lang.com/), [Less](http://lesscss.org/) and [SASS](http://sass-lang.com/) as the most popular choices. In my opinion the latter is the most advanced, and if you want to learn one, you should really look deeper into SASS.

### Installation and First Run

SASS is a tool writen in Ruby language, so that it's required to be installed on your machine first. If may want to install it manually or via package manager (eg. `apt-get`) but keep in mind that SASS has [dropped support for
oder Ruby versions](http://blog.sass-lang.com/posts/560719), so make sure yours is relatively new.

Wit Ruby installed, the installation of SASS is as easy as it can get:

    $ gem install sass
    Fetching: sass-3.4.22.gem (100%)
    Successfully installed sass-3.4.22
    Parsing documentation for sass-3.4.22
    Installing ri documentation for sass-3.4.22
    Done installing documentation for sass after 3 seconds
    1 gem installed

Now you can verify that it's available via command line:

    $ sass --version
    Sass 3.4.22 (Selective Steve)

To see SASS in practice, create a simple file called `src.sass`:

    $mycol:"red"

    .my-class
      color: $mycol

Then call:

    $ sass src.sass out.css

This command creates two files: `out.css` and `out.css.map`. The output style looks like this:

    $ cat out.css
    .my-class {
      color: "red"; }

    /*# sourceMappingURL=out.css.map */

### Two syntaxes

SASS has its own, native syntax that is a bit different from CSS. It is based on indentation instead of brackets (much like Python programming language) and avoids semicolons at the end of the lines. It takes time to get used to, but with this notation the code looks much clearer. The source files using this syntax should have `.sass` extension.

If, on the other hand, you're not a fan of this Python-like style, you may stick with something that resembles good old CSS. The files have to have `.scss` extension, and you can use brackets, semicolons as you would in CSS.

### Basic features

- **Variables**

The first thing you should start using with CSS preprocessing are variables. This is the most basic, but also the coolest features that really save much of your time. You don't have to copy eg. colour definitions across multiple files, classes, etc. More importantly, if you want to change that color you need to edit it just in a single place. You've already had a chance to see it working in the _First run_ example few lines above.

- **Nesting**

Another thing that helps you organize code and save on key strokes is style nesting. A perfect example is styling a group of elements, such as lists. Your input looks like this:

    .container
      background-color: '#121212'

      ol
        list-style: none

        li
          color: 'blue'

And output:

    .container {
      background-color: "#121212"; }
      .container ol {
        list-style: none; }
        .container ol li {
          color: "blue"; }

    /*# sourceMappingURL=nest.css.map */

- **Imports**

Another feature that help you manage your code is importing files into another. This way you can have a group of selectors describing a particular part of your page. You can, obviously, have separate CSS files as well, but with preprocessing you produce a single output file which limits the number of resources requestes. Example:

    // first.sass
    .first-class
      background-color: 'red'

    @import 'second'

    // second.sass
    .second-class
      color: 'pink'

Output of `sass first.sass output.css`:

    .first-class {
      background-color: "red"; }

    .second-class {
      color: "pink"; }

    /*# sourceMappingURL=first.css.map */

- **Mixins**

One of more complicated features of SASS are mixins, which act just like functions in other programming languages. It's basic syntax allows you to define a group of features that can be injected into multiple places in your code:

    @mixin mymix
      color: 'red'
      font-size: 2em

    .classA
      background-color: 'green'
      @include mymix

    .classB
      background-color: 'yellow'
      @include mymix

Output:

    .classA {
      background-color: "green";
      color: "red";
      font-size: 2em; }

    .classB {
      background-color: "yellow";
      color: "red";
      font-size: 2em; }

While this is cool, the real power of mixins is in the possibility to pass parameters to alter its content:

    @mixin mymix($fontSize: 3em)
      color: 'red'
      font-size: $fontSize

    .classA
      background-color: 'green'
      @include mymix

    .classB
      background-color: 'yellow'
      @include mymix(1.2rem)

Output:

    .classA {
      background-color: "green";
      color: "red";
      font-size: 3em; }

    .classB {
      background-color: "yellow";
      color: "red";
      font-size: 1.2rem; }

Note, that mixinx can (don't have to) have default values for its parameters. If the value is not provided, the default one is used.

- **Inheritance**

The last important feature that can be used in SASS is inheritance, which is quite easy compilation step, yet can really make your life easier. If you have some two classes, and one implies the other (eg. all `.cool-button`s are `.button`s), you want to use inheritance. How is it different than extending a mixin? If you create a classB that is exended by classA, and at some point (in some other place of code) you add some attributes to classA, it is automatically reflected in classB. Also, when you create some nested selector (eg. `.classA img`) it is also reflected in `.classB img`. You cannot do either using mixins, while with inheritance is quite easy:

    .button
      background-color: 'red'

    .cool-button
      @extend .button

    .button label
      color: 'grey'

Output:

    .button, .cool-button {
      background-color: "red"; }

    .button label, .cool-button label {
      color: "grey"; }

As you can see, SASS just replaces `.button` with selector group `.button, .cool-button` so that all properties appear in both selectors. This way you don't need to remember it yourself, and you explicitly define that those classes have a relation and not just _happen to appear in the same places_.

### Summary

As you can see, SASS is a rather easy tool which at the same time is quite powerful. Once you get used to its syntax and learn its cool features, you are probably not going to look back at traditional ways of creating and managing your styles. And you shouldn't!