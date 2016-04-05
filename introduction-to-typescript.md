# Introduction to TypeScript

Recently I've started looking into the details of Angular 2, and as you can see on the official website, those guys are moving towards Typescript. At first I was a bit skeptical, as I didn't want to learn another syntax just as I got comfortable with ES6 features. Fortunately I gave it a try, and as it turned out, it's not that far from we JS devs already know.

### Relation to JavaScript

It all must come down to plain old JS. And it does, to ES5. As I've already mentioned before, the support for ES6 features is stil so limited, that you just cannot rely on your code being ran correctly. This is why we need Babel to transcript ES6 to ES5, and we also need that additional step with Typescript.

So what exactly is that Typescript has, that you should want to try it? It is basically a ES2016 (aka ES7) JavaScript with the addition of types. Wait, what? So we're not going to write such dynamic code as in JS? Nope. And even though you may not feel it straightaway, it is a good thing.

Remember, that with a dynamic language as JS, you need to keep so many things in your head. You need to remember, that this given function returns a number, which will be used in that place. Or, even worse, you know that the function returns a string or `undefined` under some circumstances. With Typescript, you delegate such things to the compiler, which warn or stop you from creating those kinds of quirks.

### First steps

First, you obviously need to have Typesccript compiler installed on your machine. Fortunately, it's very simple and easy:

    $ npm install -g typesctipt
    
And now you can start creating your first `.ts` files:

    // hello-world.ts
    var a = 'Hello',
        b = 'world';
    console.log(`${a} ${b}!`);

Then you can compile it to JS using a `tsc` command (which we've just installed):

    $ tsc hello-world.ts # creates hello-world.js file
    
and run it using node:

    $ node hello-world.js
    Hello world!

As you can see, you could have that `hello-world.ts` file created initially as a plain JS, just with different extension. Even so, the compilator does something:

    var a = 'Hello', b = 'world';
    console.log(a + " " + b + "!");

As you can see, the output code is ES5, which is pretty safe to run everywhere.

### Using basic types

### Interfaces and classes



TODO:

- advantages of types
- basic types
- interfaces
- classes
