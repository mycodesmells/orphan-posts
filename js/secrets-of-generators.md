# Secrets of Java Script generators

ECMA Script 6 has introduced numerous new features to Java Script language, such as arrow functions, block scope variables or classes. One of the lesser known additions to the language are generators. What are those?

### Generators vs ordinary functions

Generator is a specific type of function, that does not return its complete result at once, but instead returns an iterator that allows you to ask for the next part of the response until there is nothing left to be returned.

For example, an array that returns a list of NBA all-time greats might look like this:

    function getNBAGreats() {
      return [
        'Michael Jordan',
        'Magic Johnson',
        'Bill Russell',
        'Kobe Bryant',
        'Oscar Robertson'
      ];
    }

The main issue with this function is that we already know that we only have five players on this list. What if you want to (spoiler alert!) _generate_ more values? This is where generators come in.

### Your first generator

In order to define a generator function you need to remember two things: first of all, you need to add an asterisk (`*`) to the `function` keyword (or before function name):

    function* firstGenerator() { ... }
    function *secondGenerator() { ... }

**Note** those two options are synonymous, but you should be consistent across your code and decide which style you want to use.

Second thing that you need to know about generator functions is that you don't `return` values, but instead you need to `yield` each part of your response:

    function* getNBAGreatsGen() {
      yield 'Michael Jordan';
      yield 'Magic Johnson';
      yield 'Bill Russell';
      yield 'Kobe Bryant';
      yield 'Oscar Robertson';
      // ... and we can have more:
      yield 'Larry Bird';
      yield 'Tim Duncan';
      yield 'Isiah Thomas';
      yield 'Shaquille O\'Neal';
      yield 'Julius Erving';
    }

But how can you use those values? There are two main ways to go. First is to retrieve all of them at once with `for ... of` loop:

    for (let p of getNBAGreatsGen()) {
      console.log(p);
    }

Second approach is a bit more complicated, but at the same time gives you much more control:

    let g = getNBAGreatsGen();
    console.log(g.next().value); // Michael Jordan
    console.log(g.next().value); // Magic Johnson
    console.log(g.next().value); // Bill Russell
    console.log(g.next().value); // Kobe Bryant
    console.log(g.next().value); // Oscar Robertson
    console.log(g.next().value); // Larry Bird
    // six is enough, no need to ask for any more!

### Infinite laziness

This might feel like an overkill, as you could just ask the first function for all players and then just slice your way into a list of six, right? There are more complicated use cases for generators, though. For example, a generator is a perfect solution for creating a unique ID. All you need to do is create... an infinite loop! Yeah, this is one of the most obvious anti-patterns of programming, but in this case is not only acceptable, but also very popular! The secret of generators is that tne _next_ value is not created unit something explicitly asks for it! Our ID generator might look like this:

    function* idGenerator() {
      let id = 0;
      while(true) {
        yield '#' + id++;
      }
    }

The problem is, that if you want to get all values (`for ... of` loop), it leads to actual infinite loop and all you can do is kill your script process. If, on the other hand, you choose to iterate over the values, you are good to go:

    let g = idGenerator();
    console.log(g.next().value); // #0
    console.log(g.next().value); // #1
    console.log(g.next().value); // #2
    console.log(g.next().value); // #3
    console.log(g.next().value); // #4

### Sending values in

Last thing that you might be interested in with generators, is that you can not only get values from them, but also send some data in. This is slightly more complicated, so don't worry if you don't get it at first.

    function* mygen() {
      let index = 1;
      while(true) {
        let reset = yield index;
        if (reset) {
          index = 1;
        } else {
          index = index * 2
        }
      }
    }

    const gen = mygen();
    console.log(gen.next().value); // 1
    console.log(gen.next().value); // 2
    console.log(gen.next().value); // 4
    console.log(gen.next(true).value); // 1
    console.log(gen.next().value); // 2

As you can see, we can send data to the generator by passing it as an argument to `next(..)` function. Now here comes the tricky part - it can be used inside generator function as it is assigned to... `yield`! If we don't pass any argument, generator continues its execution - it returns `index` variable value. If, on the other hand, we send something in (`next(true)`), `yield` is replaced by `true`, and the generator checks realizes that it should reset itself. What happens next? Since `yield` was used as data receiver in this loop run, nothing was returned, so our generator continues its work until it reaches a `yield` responsible for publishing something. In our case, after the reset, `index` is equal to 1 and this is what is being returned.

### Can I use it already?

Yes, you guessed it right... Unless you don't have to support Internet Explorer 11, Android 5 and iOS 9 (yeah, right!), you are safe([view details](http://kangax.github.io/compat-table/es6/#test-generators)). Once again we don't live in the future yet and need to hope that generators will be fully supported sooner than later.
