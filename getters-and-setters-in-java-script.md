# Getters and Setters in Java Script

Java Script is all about being dynamic - you can reassign values of various types to the same variable and as long as you have control over it, you are fine. Unfortunately, once your project reaches a certain size and complexity, you are no longer able to keep everything in your head. ES6 introduces a feature that has been designed to help you keep your variables consistent - getters and setters.

### Is this new?

This might be a question you want to ask right now. But first, let's start with the idea of private properties of an object. Imagine you want to describe a person object, you can write:

    let person = {
        name: 'John Doe',
        age: 21
    };

But with such implementation noone protects you from a mean hack like this:

    person.age = 'green';

In order to keep an attribute as private (ie. not accessible from outside `person` object), you can create a class function:

    const Person = function() {
        let name = 'John Doe';
        let age = 21;        
    }

But how can you read them, or change their values? You can create a getter and setter, which are just plain Java Script functions that happen to access those props:

    const Person = function() {
        ...
        this.getName = function() {
            return name;
        }
        this.setName = function(newName) {
            name = newName;
        }
        ...
    }

This is also a perfect place for your input validation, so that nobody will set a person's age to value _green_. The only problem is that you need to use artificial functions, their names are pretty verbose, which lead you to have more Java-feel than JS's.

### Indeed it is

So what advantage can ES6 `get` and `set` methods bring? They combine the usual ease of use (simple assignments and reads) with function validation. For example, we'd like to have both first and last name starting with a capital letter (little assumption: person has one first name and last-name):

    const toTitleCase = (string) => string.slice(0,1).toUpperCase() + string.slice(1);

    class Person {
        get name() {
            return this._name;
        }

        set name(newName) {
            this._name = newName.split(' ').map(toTitleCase).join(' ');
        }
    }

**Note** that we are keeping _real_ name value in a `_name` variable - this is far from perfect, but there is a way to overcome this (will tackle that later on). The usage of our `Person` class looks like this:

    let p = new Person();
    p.name = 'pawel slomka';
    console.log(p.name); // "Pawel Slomka"

How awesome is that?!    

### What about non-classes?

It's easy to create getters and setters in ES6 classes, but what about copying this behavior to our original, plain object? It is also possible there, and requires just a little bit more effort, thanks to `Object.defineProperty` method:

    let person = {};
    Object.defineProperty(person, 'name', {
        get: function() {
            return this._name;
        },
        set: function(n) {
            this._name = n.split(' ').map(toTitleCase).join(' ');
        }
    })


    person.name = 'pawel slomka';
    console.log(person.name); // "Pawel Slomka"

As you can see, we are still using `_name` attribute to keep the value. This actually allows someone to access and modify that one. Can we make our fields _really private_ then?

### Hiding privates in classes

In order to keep our variables safe, we need to keep them in a function closure. Then we need an additional trick, to make our class look like an usual class to the outside world:

    const Person = (function() {

        let name = '';

        class Person {
            get name() {
                return name;
            }
            set name(newName) {
                name = newName.split(' ').map(toTitleCase).join(' ');
            }
        }

        return Person;
    })();

With this solution, we are using a local `name` variable to keep the value, but since our `const Person` is an immediate anonymous function, there is no possibility to enter the scope and change it manually. The usage is identical as previously:

    const p = new Person();
    p.name = 'pawel slomka';
    console.log(p.name); // "Pawel Slomka"

### Summary

As you can see, `get` and `set` methods are very easy, yet they allow you to keep your class/object usage as simple as usual, with validation functions protecting its internal state consistency.  
