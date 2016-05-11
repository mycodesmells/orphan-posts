# Monitor Your npm dependencies

It's easy to start creating a new application using npm. There is a plethora of packages (which [not always is a good thing](http://mycodesmells.com/post/3-rules-that-will-blow-your-js-mind/?platform=hootsuite)), so that you can focus on delivering a business value almost from the very first line of your code. But as you develop your part, your dependencies change as well. How do you keep track of them, so that you get rid of their bugs and see their latest features?

There are a couple of tools that you should really look into, regarding this topic: one is perfect for monitoring dependencies' versions, the other one is pretty useful to make usre all your dependencies are in place so that you don't have any surprises with your CI builds.

### npm-check

This is a must-have in your repertoire as a JS dev, because you must realize that you should check for dependencies' updates every now and then. Now there might be a good time to tell you this: forget about _don't test external libraries_ rule. You should, because that's how you deliver a perfect product. Your customer won't accept that your app crashes _because jQuery has bugs_. They don't care about jQuery, lodash, or Angular (yeah even that!). If you expect your servce to return a list filtered using lodash, test the result manually. That way if you ever upgrade its version and it suddenly is a mess, you know that beforehand.

Installation and use of _npm-check_ is very easy. As with almost all npm-based tools you should install it globally (no need to have it as a local dependency in your project):

    npm install --global npm-check

And now you can use it in your project's root directory to see how many dependencies are out of date:

    $ npm-check

    serve-favicon   NOTUSED?  Possibly never referenced in the code.
                             npm uninstall --save serve-favicon to remove.

    mongoose        UPDATE!   Your local install is out of date. http://mongoosejs.com
                             npm install mongoose to go from 4.4.7 to 4.4.12

    nodemailer      MINOR UP  Minor update available: http://nodemailer.com/
                             npm install --save nodemailer@2.3.1 to go from 2.0.0 to 2.3.1

This example output show you how old your dependencies are. You can see that _npm-check_ notifies you when one of them is not probably not used, which are _slightly_ outdated (_UPDATE!_) and which are really old (_MINOR UP_).

### dependency-check

This is a similar tool, but approaches the problem from the other side. It checks if the dependencies you declare are used in your code, but this time you can specify a different entry point than defined in `package.json` configuration file. Installation is rather standard:

    npm install --global dependency-check

And its basic use tells you if all your `require(..)` calls are reaching to dependencies defined in `package.json`:

    $ dependency-check package.json --entry bin/server.js
    Success! All dependencies used in the code are listed in package.json

As you can see, the `--entry` parameter allowed us to define our entry point, even if it's not defined anywhere else. You can take it a step forward and check which declared deps are not used at all:

    $ dependency-check package.json --entry bin/server.js --unused
    Fail! Modules in package.json not used in code: kerberos, moment, chai, gulp, gulp-jshint, gulp-mocha, istanbul, jshint, jshint-stylish, mocha, nodemon, sinon, supertest

Now that is a pretty long list, huh? But wait a sec... We are definidely using `gulp` in our project, but it's not accessed via our entry point of `bin/server.js`! What now? It's easy - just skip your dev-dependencies:

    $ dependency-check package.json --entry bin/server.js --unused --no-dev
    Fail! Modules in package.json not used in code: kerberos, moment

And now you know what can be safely deleted from `package.json`.

Unfortunately, dependency-check has one major flaw: as of version `2.5.1` (latest at this point), **it still does not support ES6 _import_ syntax**. This is a bummer, as you can only use it on your destination directory and not your source.

### Good Old npm

The last _tool_ worth mentioning, is npm itself. At any point of your development you can run a simple command:

    npm ls

to see an output like this:

    $ npm ls
    myproject@1.0.0 /path/to/myproject
    ...
    ├─┬ express@4.13.4
    │ ├─┬ accepts@1.2.13
    │ │ ├─┬ mime-types@2.1.10
    │ │ │ └── mime-db@1.22.0
    │ │ └── negotiator@0.5.3
    │ ├── array-flatten@1.1.1
    ...
    │ └── vary@1.0.1
    ├─┬ express-handlebars@3.0.0
    ...
    ├── lodash@4.6.1
    ├── moment@2.12.0
    ├─┬ mongoose@4.4.12
    ...

Now this does not provide you with much information, but it changes when you install some package without `--save` option:

    $ npm install underscore
    $ npm ls
    myproject@1.0.0 /path/to/myproject
    ...
    ├─┬ express@4.13.4
    ...

    npm ERR! extraneous: underscore@1.8.3 /path/to/myproject/node_modules/underscore

This command also notifies you if you have missing dependency, even if it's not a top-level one, but required by something:

    $ rm -rf node_modules/async
    $ npm ls
    myproject@1.0.0 /path/to/myproject
    ...
    ├─┬ express@4.13.4
    ...

    npm ERR! missing: async@^1.4.0, required by handlebars@4.0.5
    npm ERR! missing: async@1.5.2, required by mongoose@4.4.12

This might help you catch any unusual things going on in your `node_modules` directory.

### Summary

Those tools are definitely something worth looking into, especially if you are working on a project that lasts longer than a couple of months. Remember that as your project grows, so do your dependency, and monitoring them should be treated as a part of your job.
