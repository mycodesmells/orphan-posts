# Testing Java Script with Mocha

Since we've already taken a look on the most popular JS testing library, Jamine, is there a point of checking out another one? Of course it is - that way you can actually choose what you like better, instead of being limited to the only one you know. Let's get started then!

### Key difference between Mocha and Jasmine

If there was one thing you should know about the difference between the two, you should understand what comes in the boxes. With Jasmine, you get basically everything. You have a test runner, assertions, mocks, spies - you probably don't need anything else. Mocha, on the other hand isn't closely as rich in content - it's just a test runner. So why on Earth would you like to use it? You can buy a PC which can do fine in general, or you can build it yourself, and it could perform perfectly for tasks you need it do do.

### Assertions - chai to the rescue

You will definitely need some assertions in your tests - it's hard to check your code's behaviour without, em... actually checking it. There are a few libraries you can use, but the most popular one is `chai`, which itself can be used with different assetion styles (expect, assert and should). If you have Jasmine background, you should start with expect, as it is the most similar.

### Spying

You are probably wondering, if spying is a built-in feature of Mocha. Of course it's not - you should use another package called `sinon`, which is a bit more powerful than Jasmine. It has spies, stubs and mocks - these are some big words, which are used on slightly different ocasions in your tests.

_Spy_ is used to monitor that given function has been called. You don't change its behaviour with spy, so that it's being called through.

_Stub_ defines the way you want to override chosen function. You don't check the calls, arguments etc, but only change what is happening and the result. 

_Mock_ combines features of previous two - you can both define the behaviour and check the calls that have been made.

### Sinon Fake Server

This is something not really related to Mocha, but more to aformetioned Sinon, but it's so cool that it deserves a mention. For more details, you may want to check out [David Tang's post](http://thejsguy.com/2015/01/12/jasmine-vs-mocha-chai-and-sinon.html).

Hope this clears the air a bit - you now know that Jasmine is not the only path you can choose when testing your Java Script project.
