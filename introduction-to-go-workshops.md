# Introduction to Go Workshops

Recently I delivered an introductory Go Lang presentation and workshops at my company. It was tergeted for those that have never had any experience with the language, so that anyone should have no problem with understanding it. There are obviously things that need to be explained, and that's the point of this post.

Presentation is available (on Github)[https://github.com/mycodesmells/go-workshops].

### Practice Over Theory

I personally do not have much experience with Go myself, and I definitely take practice over theory any day of the week. This is why those workshops were designed in a Test Driven Development-like style - it consists of eight practical tasks that take you step by step, deeper and deeper, so that at the end you should be able to create you first Go app.

### Task 1 - Installation

This is something I had surprisingly much trouble with, when I was starting out. Once you download and install Go on your machine, you need to set some environmental variables: 

- `$GOROOT` is where your Go in installed, 
- `$GOPATH` is where your code (projects) reside,
- `$GOBIN` is where your compiled binaries are to be created

To make things easier, you should have `$GOROOT/bin` and `$GOBIN` added to your `$PATH`. That way you not only can use `go` command from anywhere, `$GOBIN` allows you to use your compiled scripts as well.

First task is just a validation of your installation.

### Task 2 - Public vs Private

Second task is designed to make you understand, that everything in Go is private (ie. not accessible from another package) unless it starts with a capital letter. In this task you should run the test and fix the code so that it compiles.

### Task 3 - Creating structs

This is another simple one - you are supposed to instantiate a struct with given values. Just create it, make sure you understand how to do it. That's all!

### Task 4 - Interfaces

Task four is a bit more difficult, because you need to check other files to see how to implement an interface. The example on slide is simple enought, but you need to understand that the function name, parameters and return types must be exactly the same. Tricky part is, that when struct implements an interface, you just implement a function. No explicit _implements_ keyword or anything. 

### Task 5 - Pointer Receivers

Building on Task 4, this time you need to remember that everything is passed by value in Go, unless you pass a pointer. While writing a function, whether as a part of an interface or not, and you want to make some persistent changes to the object, you just need to add and asterisk. Task five checks if you understand it.

### Task 6 - Goroutines: WaitGroup

The rest of the tasks describe managing goroutines. But this task is an unusual one, as it is not a test, but just a script run (`go run`). It is self explanatory, as you need to create a `sync.WaitGroup` to... wait on all goroutines to finish before going on with script execution. 

### Task 7a - Channels Intro

First of the tasks connected with channels describe the situation, where you need to put something into an unbuffered channel to proceed. Just throw something it, because if you don't, you'll freeze forever.

### Task 7b - Implementing Timeout

The last one is probably the most difficult. For simpler test output, one of test functions is commented out - you should focus on one at a time. The idea of a task is to understand how `select` works - the moment any of the channels have some message in, an action is selected. First test is rather easy, so for second one you are expected to implement a timeout - after some arbitrary time (it's your call, I recommend 100ms or something), another action should be executed and you should quit the `select` block.

### Summary

I welcome any comments or suggested upgrades, feel free to share this with anyone that wants to learn Go. I really hope that helps! 

