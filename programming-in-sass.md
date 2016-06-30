# Programming in Sass

As you have already seen, Sass is quite easy to start working with. But is that it? Is it just a simple preprocessor that allows variables, nesting and little more? Obviously not (otherwise this would be a short and dumb post)! Let's take a look on some slightly more complex features and how to use them in practice.

In my opinion the main thing that allows something to be called _a programming language_ is the ability to control flow and make decisions during runtime. Up until now we saw just some basic constructions that were mainly based on copying parts of text between places in order to build an output CSS. Now, we'll see what else Sass brings to the table.

### Data types

In Sass we are dealing with five basic data types: strings, numbers, colors, lists and booleans. Most of them are pretty self-explanatory, just a list may require a bit more, and the best way to present them is with some examples. One which is very common is a attribute that has more than one element, such as:

    border: solid 1px red

In this case, `border` property has a value represented with a list of three. A list can be expressed with its elements separated with spaces (like here), or with a comma (like in `font-family: FontA, FontB, FontC`). And lists are not limited to the attributes only, as you will see later on.

### Basic operations

Limiting variables just to inserting a named value in specific places would be very annoying, but fortunately there are some operations that can be done with them as well. For instance, if you want to create a container that has a given number of columns, each having specific width, you can to the following:

    $colCount: 10
    $colWidth: 15px

    .main-container
      width: $colCount * $colWidth
      padding: 10px

An output in CSS would look like this:

    .main-container {
      width: 150px;
      padding: 10px; }

When it comes to string manipulation, the most common operation is concatenation. You can join texts using three operators, which may be very useful at one point:

    Hello+World => HelloWorld
    Hello-World => Hello-World
    Hello/World => Hello/World

On top of of that, there are a number of functions that can be applied to certain data types, so that those values can be manipulated. For example you can round a numeric value with `round($value)` or invert a color with `invert($color)`. You can see a complete list of built-in functions is available in [Sass' documentation](http://sass-lang.com/documentation/Sass/Script/Functions.html).

If you need to performs some unique action, and there is no ready function that helps with it, you can create your own with `@function`:

    @function calculateTableWidth($colWidth, $colPadd, $colCount) {
        @return ($colWidth + $colPadd) * $colCount;
    }

### Flow control directives

Two main ways to control _flow_ in any programming language are conditional expressions and loops. First, often called just _ifs_ take an boolean argument and based on its value perform (or don't) some specific action. In case of Sass, `@if` defines if some styles should be applied or not:

    @if $colAlpha < 0.2 {
        background-color: black;
    } else {
        background-color: gray;
    }

Loops are slightly more powerful in terms of generating lots of CSS code, and are more popular because they allow to perform some advanced tricks. For example, if you have some kind of score (eg. ranking of books in your e-library) and want to display its value using images (each one representing given number of stars), you can do the following using `@for` loop:

    @for $i from 1 through 5 {
        .rating-#{$i} {
            background-image: url(/images/rating-#{$i}.png);
        }
    }

You can also use `@each` loop for applying some attributes to a list of elements within some main component, without having to repeat your precious keystrokes:

    @each $el in menu, content, footer, some-class {
        #container .${el} {
            color: pink;
        }
    }

### Summary

As you can see, Sass is much more than an enhanced CSS syntax. I dare you to look deeper into it, so that creating your styles faster and smarter. You have to invest some of your time to master it, but it will pay off sooner rather than later!