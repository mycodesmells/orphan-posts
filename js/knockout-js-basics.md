# Knockout JS Basics

In today's world, you just can't have enough JavaScript frameworks and libraries, right? You have your classic React and Angular, many people still building their apps on jQuery. But there are so many more... One of them is Knockout JS, which is much simpler than big-but-heavy frameworks, but at the same time make you much very comfortable. Let's take a look on how we can craete a simple page using this library.

### What is Knockout?

Knockout is a very lightweight MVVM (_Model_-_View_-_View/Model_) JavaScript framework that allows you to populate your HTML templates with data stored in JS, create data bindings, handle events etc. You don't have to worry about replacing data manually, but at the same time you don't have to worry about your application becoming too big too fast.

### Basic syntax

In Knockout it all starts with a model function. This represents a class, so it is instantiated with `new` keyword. Once it is created, it needs to be bound to the HTML using `applyBindings` function:

    function Model() {
      ...
    }
    ko.applyBindings(new Model());

So what goes into our `Model`? We can start with something easy, like binding constant values. We do that with two steps. First, we need to set some value as a property of our `Model`, and then within our HTML template add `data-bind` attribute. Its value is similar to JSON syntax, just without braces. In the most basic case, we'd like to have some string value printed as a text-content of a `<span>` tag.

    // JS
    function Model() {
      this.constanValue = 'There is no spoon';
    }

    // HTML
    <p>
      Known fact: <span data-bind="text: constantValue">
    </p>

Having done this, we can see our value inserted into HTML tag.

This looks great, but it not yet so powerful, as we cannot edit the value. It is possible, obviously, via Knockout's `observable` function. To see that we can actually edit such value, we need to bind it twice - to some displaying tag (such as `<span>`) and to some input:

    // JS
    function Model() {
      ...
      this.someVariable = ko.observable("initial-value");
    }

    // HTML
    <p>
      Changing value: <span data-bind="text: someVariable">
    </p>
    <p>
      Edit: <input data-bind="value: someVariable" />
    </p>

We can also get and set our dynamic value programatically and see the change reflected on the page. Since the value is created with `observable` it is now a function, so we need to use it like one:

    this.someVariable();            // getting
    this.someVariable("new value"); // setting

Of course, we can do much more than just editing and displaying variables values in the DOM. We can, for example, bind some button with an action declared in the `Model`:

    // JS
    function Model() {
      ...
      this.reverseValue = function() {
        var current = this.someVariable();
        var reversed = current.split("").reverse().join("");
        this.someVariable(reversed);
      }
    }

    // HTML
    <button type="button" name="button" data-bind="click: reverseValue">Reverse</button>

Last thing we really need to cover is handling collections, such as arrays. Very often you need to display a group of values on the page, in a form of some list of table, and Knockout is a perfect solution to do just that. You just need to bind your array via `foreach` bind property, then use `$data` variable to access each item and that's it:

    // JS
    function Model() {
      ...
      this.players = ['Tim Duncan', 'Kobe Bryant', 'Ray Allen'];
    }

    // HTML
    <ul data-bind="foreach: players">
      <li data-bind="text: $data"></li>
    </ul>

If you have a more complicated collection of objects and want to access each item's properties separately, it looks even better:

    // JS
    function Model() {
      ...
      this.players = [{
        name: 'Tim Duncan',
        championships: 5
      }, {
        name: 'Kobe Bryant',
        championships: 5
      }, {
        name: 'Ray Allen',
        championships: 2
      }];
    }

    // HTML
    <ul data-bind="foreach: players">
      <li>
        <span data-bind="text: name"></span>
        won
        <span data-bind="text: championships"></span>
        championships
      </li>
    </ul>

All that was available for single variables is obviously available here as well, so that you can make object properties as `observable`, change their values etc.

### Summary

As you can see, Knockout is very convenient, easy to use and at the same time very useful library that can come in handy, especially in smaller projects where React or Angular might be an overkill. It does not require any additional dependencies, the syntax is simple and it is very clear what is bound to what, just by looking on attributes of HTML elements. I highly recommend to get to know Knockout, mainly for its simplicity which can very often be just the perfect solution for your small web app.
