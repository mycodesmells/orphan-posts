# Proxies in Java Script

Being a Java Script developer, in most cases you are probably happy with how dynamic the language is. You are not bound to any type restrictions, your object can come in all shapes and sizes. And, which is important, you can change their structure at runtime. What if you want to create (or read) a nested one, but don't want to make a null-check every second line? Enter ES6 proxies.

### What is Proxy?

In ECMA Script 6, _proxy_ has exactly the same meaning as anywhere else - it is some kind of an entity that stands between a user and an item they want to use. It can be used to many things, and their important feature is that they don't change code (and therefore behavior) of that destination object. This opens a lot of possible use cases in which we could utilize them.

Technically speaking, a proxy needs to things to work: the object that we want to _hide_ from their users, and the definition of the way we want to get and set attributes of that object. The most basic Proxy (that doesn't do anything useful) looks like this:

    const proxy = new Proxy(dstObject, {
        get: function(object, property) {
            return object[property];
        },
        set: function(object, property, value) {
            object[property] = value;
        }
    });

Alternatively, you can proxy a standalone function:

    let doStuff = () => {};
    doStuff = new Proxy(doStuff, {
        apply: function(fun, thisArg, args) {
            console.log('It does something!');
        }
    });

### Logging

First use case you can consider, is loggging access to the object properties:

    function Person() {
        return new Proxy({}, {
            get: function(object, property) {
                console.log(`Getting property "${property}" (${object[property]}).`);
                return object[property];
            },
            set: function(object, property, value) {
                console.log(`Setting property "${property}" (${object[property]} -> ${value}).`);
                object[property] = value;
            }
        });
    }

    const p = new Person();
    p.name = 'Joe Smart'; // log: Setting property "name" (undefined -> Joe Smart).
    console.log(p.name); // log: Getting property "name" (Joe Smart).
    // Joe Smart

### Monitoring

Another thing you might want to do with Proxy is measuring your function performance:

    function doStuff() {
        let i = 0;
        while (i < 100000000) {
            i++;
        }
    };

    doStuff = new Proxy(doStuff, {
        apply: function(fun, thisArg, args) {
            const timerName = `${fun.name} execution time`;
            console.time(timerName);
            fun.apply(thisArg, args);
            console.timeEnd(timerName);
        }
    });

    doStuff(); // log: doStuff execution time: 52.143ms

### Creating nested structures

Another use case you may consider is, what has already been mentioned, creating nested structures without worrying about null-checks. You can achieve it with a following Proxy:

    const EMPTY_OBJECT = {};
    const COOL_OBJECT = new Proxy(EMPTY_OBJECT, {
        get: function(object, property) {
            if (!object[property]) {
                object[property] = COOL_OBJECT;
            }
            return object[property];
        },
        set: function(object, property, value) {
            object[property] = value;
        }
    });

    const e = EMPTY_OBJECT;
    const c = COOL_OBJECT;
    e.some.nested.structure.that.doesnt.exist = 'oops'; // TypeError: Cannot read property 'nested' of undefined
    c.some.nested.structure.that.doesnt.exist = 'yeah';
    console.log(c.some.nested.structure.that.doesnt.exist); // "yeah"

Important thing in this solution is a null-check in `get` method - if a property doesn't exist, we assign another `COOL_OBJECT` is assigned. Thanks to this, we propagate our proxied objects to the nested attributes.

### Performance issue

Proxies can be very useful, both from code-writing perspective, and for monitoring at runtime. The main disadvantage, though, is that they affect your code performance significantly:

    function raw() {};

    let proxied = new Proxy(raw, {
        apply: function(fun, thisArg, args) {
            fun.apply(thisArg, args);
        }
    });

    const RANGE = 100000000
    let i = 0;
    console.time('raw');
    while(i < RANGE) {
        raw();
        i++;
    }
    console.timeEnd('raw');

    let j = 0;
    console.time('proxied');
    while(j < RANGE) {
        proxied();
        j++;
    }
    console.timeEnd('proxied');
    // "raw: 987.306ms"
    // "proxied: 19251.371ms"

### Summary

Proxies are not yet fully supported, but as with many ES6 features we just need to be patient. Although they provide some great features to your code (without modifying your business logic), performance overhead is sometimes too huge. Anyway, it's good to know such a solution, in case you need to wrap your code with such middleware.
