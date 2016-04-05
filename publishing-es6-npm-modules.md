# Publishing ES6 Modules to NPM

Once you go ES6, you hate to go back, right? It's so cool to use all the latest features that make your code prettier, more consise and easy to manage. But the reality is unfortunately brutal, as ES6 is not fully supported neither in browsers nor in Node JS. So are we supposed to use ES5 forever in our npm modules? Not at all! Let's take a look what you need to do to write your package in ES6 and have it published.

### Why ES6 is not safe yet

If you write your own application, you can set up your development environment to have it transpled to ES5 which is fine. But if you want to go public with your package, this might raise some problems - not everyone is writing in ES6, so that your code might not be useful. Publishing code in ES5 is much safer, but we would really love to use that _let_ keyword...

The easiest solution is transpiling using one of the tools available and publishing the output. My personal favourite is (babel)[https://babeljs.io/], which can be used like this:

    babel -c -d ./dist ./src/ --presets es2015,stage-2
    
This takes all code from `./src` directory, transpiles it and outputs it into `./dist`. To be able to use a command above, you obviously need to have babel installed, alongiside wit two additions:

    npm install --save-dev babel-cli babel-preset-es2015 babel-preset-stage-2

### Running transpilation automatically

This way you are killing two birds with one stone - you write code in ES6, which is available as ES5 to everyone else. There is one catch, though: you can't possibly remember to transpile your code every time you make a change. What now? Yes, `package.json` to the rescue! You can make use of `scripts` property and run it every time we publish the package. To do this, you need to define our babel script as `prepublish`:

    ...
    "scripts": {
      "prepublish": "babel -c -d ./dist ./src/ --presets es2015,stage-2",
    },
    ...
    
You might be wondering, if you need to have that transpiled code in your repository after publishing. You're right - you don't! You can have it removed after publishing package to npm:

    "scripts": {
      "prepublish": "babel -c -d ./dist ./src/ --presets es2015,stage-2",
      "postpublish": "rm -r ./dist"
    },

### ES5 as an entry point

When somebody requires your package, they access its entry point, which is defined as `main` in `package.json`. Knowing that, you need to select a transpiled file in that property, even though it might not exist at the moment:

    ...
    "main": "./lib/index.js",
    ...
    
### Ignored files or not?

The last problem we face when developing code in ES6 and transpiling it, is ignoring generated code. It should be listed in `.gitignore` file, as you don't need to have it in your repository. But this raises a problem when publishing it to npm. By default, all files from `.gitignore` are not included in the output npm package. To override this behaviour, you need to create another ignore file, this time called `.npmignore` and place there everything that should be ignored - if you don't define your `./dist` directory there (and you shouldn't), it will be available.

I hope you find it helpful and you now feel encouraged not to abandon your ES6 skills while creating your latest package. Good luck!

