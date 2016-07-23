# Why Currying?

Functional programming becomes more and more popular last few years. Like all concepts in programming, it might feel odd and you might not know how and when to start. Let's take a look on one of the most common tools used by FP passionates - currying.

By the definition, currying is so simple, that it might be hard to believe that it can be useful at all. The idea is to take a multi-argument function and _divide_ it into a chain of functions, each taking one parameter and returning the next element in that chain. And that's it! Simple, yet powerful. How come?

### Simple example

Imagine we want to create a simple function that produces some logger messages to be printed in the console or stored into some log files. The function should receive one parameter for a level of log message (_DEBUG_, _INFO_, _WARN_, _ERROR_) and the message itself:

    function logMessage(level, message) {
        return `[${level}] ${message}`;
    }

Now, every time you want to log a message, you need to provide both parameters:

    logMessage('DEBUG', 'some message'); // "[DEBUG] some message"
    logMessage('ERROR', 'major crash!'); // "[ERROR] major crash!"

It might not seem too complicated, but after some time you might get tired of remembering what levels are available, or you may even make a small typo which would cause all your `ERORR` log messages not stored correctly, lost or something like this.

### Cleaning it up

With currying, you can create a bunch of similar variants of a function, which is perfect for you use case. First we need to change our `logMessage` to make a curried version:

    function logMessageCurrying(level) {
        return function(message) {
            return `[${level}] ${message}`;
        }
    }

Then, we can create four separate functions for specific log levels:

    var logMessageInfo = logMessageCurrying('INFO');
    var logMessageDebug = logMessageCurrying('DEBUG');
    var logMessageWarn = logMessageCurrying('WARN');
    var logMessageError = logMessageCurrying('ERROR');

Using them now requires much less hassle:

    logMessageDebug('some message'); // "[DEBUG] some message"
    logMessageError('major crash!'); // "[ERROR] major crash!"

It is important to remember, that the order of parameters in curried version of a function is critical - function that are nested deeper should take parameters that change more frequently. Imagine how dumb our `logMessageCurrying` would be if we had our parameters reversed:

     function logMessageCurryingDoneWrong(message) {
         return function(level) {
             return `[${level}] ${message}`;
         }
     }

     logSomeMessage = logMessageCurryingDoneWrong('some message');
     logSomeMessage('DEBUG'); // "[DEBUG] some message"

You would most likely use that `logSomeMessage(..)` function once and the whole process would be counter-productive, as you would need to create an additional line of code for every unique log message.

### Stream processing

All that was above looks like just _a cooler way of doing stuff_, but once you get to processing streams or lists of data, you will definitely feel the difference. If you had a list of messages which you would like to log, with a traditional approach you would need to write:

    let messages = ['message #1', 'message #2', 'message #3'];

    messages.forEach((msg) => logMessage('INFO', msg));

While with currying, you can make use of the fact, that `forEach(..)` (or `map(..)`, etc.) expect to be called with a single-parameter function. While iterating over `messages` list, the inner function is being called with each element, so that all we need to do to replicate the code above is:

    messages.forEach(logMessageInfo);

Furthermore, with such stream processing, you might save space even without any helper functions, especially if you have more than two parameters in the original function:

    // original function
    function logUserMessage(level, user, message) {
        return `[${level}] - ${user} - ${message}`;
    }
    // processing with an original function
    messages.forEach((msg) => logUserMessage('INFO', 'slomek', msg));

    // curried function
    function logUserMessageCurried(level) {
        return function(user) {
            return function(message) {
                return `[${level}] - ${user} - ${message}`;
            };
        };
    }
    // curried processing
    messages.forEach(logUserMessageCurried('INFO')('slomek'));

### Lots of manual refactoring

You might wonder, how much time it would take you to change your ten-parameter function into a curried one, right? Fortunately, you don't have to do it by hand! Some npm packages can do that for you, for example [lodash](https://lodash.com/) or [Ramda](http://ramdajs.com/):

    const _l = require('lodash');
    const R = require('ramda');

    function oldFun(a, b, c, d, e, f, g, h, i, j) {
        console.log(a, b, c, d, e, f, g, h, i, j);
    }

    let newFunLodash = _l.curry(oldFun);
    let newFunRamda = R.curry(oldFun);

    oldFun('q','w','e','r','t','y','u','i','o','p');
    // q w e r t y u i o p
    newFunLodash('q')('w')('e')('r')('t')('y')('u')('i')('o')('p');
    // q w e r t y u i o p
    newFunRamda('q')('w')('e')('r')('t')('y')('u')('i')('o')('p');
    // q w e r t y u i o p
