# Fetch API

How many times have you made an HTTP request in your Java Script application? Quite a lot, right? But how many times have you done that without any framework/package? I thought so. Fortunately, changes are coming with a brand new fetch API.

### The old way

With a popularity of jQuery, you might be thinking that calling your REST backend is exclusively done with `$.ajax(..)`. But how it can be done with plain Java Script? OK, but dont' get too scared (source - [http://youmightnotneedjquery.com/](http://youmightnotneedjquery.com/#request), BTW check this page out):

    var request = new XMLHttpRequest();
    request.open('GET', '/my/url', true);

    request.onload = function() {
      if (request.status >= 200 && request.status < 400) {
        // Success!
        var resp = request.responseText;
      } else {
        // We reached our target server, but it returned an error

      }
    };

    request.onerror = function() {
      // There was a connection error of some sort
    };

    request.send();

### The new way

Our brand new friend, `fetch(..)` function returns a Promise, so you don't need to define any callbacks (those are so 20th century, right?). Then, once we get the response from our server, the response itself is a Promise and has to be treated this way. It might feel strange, but thanks to this the most simple example looks like a cool chain of functions (popular in Functional Programming):

    fetch('http://localhost:3000/people')
        .then(res => res.json())
        .then((res) => console.info(res));

As you can see, fetch returns a Promise, so the first `then` extracts its JSON content, which returns another Promise. The second time we call `then` we actually get that JSON and we can do whatever we want, in this case - print it to the console.

You can, obviously, do much more that make a GET request without any HTTP headers. In fact, an alternative to `fetch(string)` is much more robust `fetch(Request)`. You can create your `Request` object with attributes like method, headers, cache and more:

    var request = new Request('http://localhost:3000/people', {
        method: 'POST',
        data: {
            "id": 21,
            "name": "Tim Duncan",
            "age": 40
        },
        headers: new Headers({
            'Content-Type': 'text/json',
        }
    });

    fetch(request).then(...)

### Handling errors

If you have any experience with Promises, you probably know that if you want to handle errors that occur in any of the chained calls, you just need to add `catch(..)` statement at the chain's end:

    fetch('http://localhost:3000/people')
        .then(res => res.json())
        .then((res) => console.info(res))
        .catch((err) => console.error('whoops:', err));

In this case, if `res.json()` or `console.info(res)` failed, you would have a clean way to handle those errors. You must remember, though, that `fetch` does not do anything about HTTP erros, for example 404 Not Found. As you may want to handle HTTP codes specifically, you need to do this manually. At the same point, you can use your status check logic to throw some specific error, which would be handled later by `catch(..)`:

    fetch('http://localhost:3000/people22')
        .then(res => {
            if(res.status !== 200) {
                throw new Error(res.statusText)
            }
            return res;
        })
        .then(res => res.json())
        .then((res) => console.info(res))
        .catch((err) => console.error('whoops:', err));

Nice, clean and easy.

### Support

Before you get your hopes too high, you must realize that the support for `fetch` in modern browsers is not yet a standard. [CanIUse.com](http://caniuse.com/#feat=fetch) suggests, that less than 60% of users have browser that fully support this feature. This is really depressing after seeing it in action, right? You can use some polyfills like [this one](https://github.com/github/fetch) of wait a bit and hope that Internet Explorer dies as soon as possible, and Safari (this one is kinda strange to me) picks it up.
