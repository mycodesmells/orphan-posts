# Organize Frontend Scripts with RequireJs

There are many ways to organize your frontend scripts. You can for example use a tool like Webpack, so that you have all of your sources merged into one large file. While this saves on HTTP requests (only one), it might not be very optimal if you have huge number of files but never use all of them. Can you load your scripts lazily? You can, for example with RequireJs.

### What is Require JS?

It a pretty cool JavaScript library, that enables you to load your scripts dynamically and lazily. You can define dependencies between files, so that they are loaded only if they are really necessary. Plus, you don't need to have a number of `<script src="..">` elements in your HTML template.

So how do you start working with it? I like to use npm for that, to make all set up fast, but you very well can have your scripts loaded manually or via Bower for example. For this example we want to create a script that loads two libraries (jQuery and lodash) and prints their versions (to make sure that they are loaded properly).

First we need to install requirejs and the two libraries:

    npm install --save requirejs jquery lodash

then, we need to create a _configuration file_. This file contains a map between file names and their locations:

    // js/requirejs-config.js
    require.config({
        paths: {
            'jquery': 'node_modules/jquery/dist/jquery.min',
            'lodash': 'node_modules/lodash/lodash.min',

            'main': 'js/main'
        }
    });

As you can see, we defined three files we want to use in our app. We actually need to create that third one:

    define('main', ['jquery', 'lodash'], function($, _){
        console.log('Loaded main script');
        console.log('jQuery:' , $.fn.jquery);
        console.log('lodash:' , _.VERSION);
    });

The file starts with a RequireJS-specific instruction, where we define the script name, then an array of dependency names (matching those from our config file) and then we define a function. The arguments that are passed into the function match our dependencies!

We didn't load our main script yet, so let's add it to our config file:

    // js/requiejs-config.js
    ...
    require(['main']);

We can pass an optional second argument, that would get `main` as an argument, so just like we got jQuery and lodash in the previous file.

The last thing to do, in order to see our example working is create an HTML template:

    <!doctype html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>RequireJS Example</title>

        <script src="node_modules/requirejs/require.js"></script>
        <script src="js/requirejs-config.js"></script>
    </head>
        ...
    </html>

The output is pretty obvious:

    Loaded main script
    jQuery: 3.0.0
    lodash: 4.13.1

### Getting cooler with data-main

We didn't want to have too many scripts in our HTML, right? Is _two_ too many? We actually can have both RequireJS and its configuration merged into one tag:

    // index.html
    ...
        <script data-main="js/requirejs-config.js?1" src="node_modules/requirejs/require.js"></script>
    ...

If we run it now we get errors:

    GET http://localhost:8080/js/js/main.js
    Uncaught Error: Script error for "main"

Why is that? As you can see by looking at the path (`/js/js/main.js`), RequireJS is looking for our scripts in a wront place now. This is because we deifned our configuration file as an entry point, so that all scripts paths need to be redefined relatively to this file:

    // js/requirejs-config.js
    require.config({
        paths: {
            'jquery': '../node_modules/jquery/dist/jquery.min',
            'lodash': '../node_modules/lodash/lodash.min',
            'main': 'main'
        }
    });
    ...

Now, everything looks perfect!

### Is it really lazy?

Let's change our code so that we use only lodash, but keep jQuery definition in our configuration:

    // js/main.js
    define('main', ['lodash'], function(_){
        console.log('Loaded main script');
        console.log('lodash:' , _.VERSION);
    });

Now, when you run the example again, you can see that jQuery has never been requested by the browser:

<img src="https://raw.githubusercontent.com/mycodesmells/orphan-posts/master/img/requirejs-lazy-loading.png"/>

This might not seem so impressive here, but imagine having dozens of files - you can really have hundreds of kilobytes saved if you load scripts only if they are truly necessary.

