# Managing dependences in Golang (1.7)

It is very difficult, not to say impossible, to build an application, a library of a tool without using any external dependencies. Even in Golang, where it is always preferred to do something yourself than use an 3rd party library, you need to use eg. database drivers and there is just no sense to reinvent the wheel. How should you manage your dependencies, so that any changes made by their authors don't break your work?

### Golang issues with dependencies

When Golang first entered the market, apparently nobody cared about versioning and dependency management. It resulted in quite a chaos now, if you really think a bit more about it. If you need to use some dependency, unlike eg. JavaScript's `npm`, you don't specify any version or git tag - just the URL (most often at GitHub) to the library, import it into your environment and use it. And it's all good at start, as you find a perfect thing that fits your needs and start using it. Unfortunately, its author may make some breaking change, so that next time you download it, you cannot be sure it works as it has before. This is madness! (Actually, I was not able to run my very first Golang project at work for this very reason)

### Good news

This issue has been brought up some time ago, and Golang 1.5 introduced an experimental solution to keep the dependencies within repository. It later evolved in 1.6 to be a flag switch (not _experimental_ anymore), and finally in 1.7 is fully supported and recommended way to manage not-your-own code.

Another great news is just how easy it is to get started with _vendoring_ - as this process is referred to by the community. So far, when you imported anything in your `*.go` file, the compiler was searching in two directories, directory here Go is installed (`$GOROOT`, most often for built-in language packages) and `$GOPATH`:

    project/file.go:9:2: cannot find package  "github.com/non-existing/dependency" in any of:
        /usr/local/go/src/github.com/non-existing/dependency (from $GOROOT)
        /home/slomek/go/src/github.com/non-existing/dependency (from $GOPATH)

With Golang 1.7, there is a third place where the dependency is seached, which is a `vendor` directory within your project:

    project/file.go:9:2: cannot find package "github.com/non-existing/dependency" in any of:
        /path/to/project/vendor/github.com/non-existing/dependency (vendor tree)
        /usr/local/go/src/github.com/non-existing/dependency (from $GOROOT)
        /home/slomek/go/src/github.com/non-existing/dependency (from $GOPATH)

This means, that if you want to make sure the dependency does not change, you just need to copy them into that `vendor` folder and you are forever safe that it will not break your build.

### Disadvantages

One thing that might make you worry with this solution, is making your repository huge fast. As you add next dependencies (which also can use another - you might want to add them as well), you end up with just a lot additional code. For example [pomodogo](https://github.com/slomek/pomodogo), a small _The Pomodoro Technique®_ tool I created has 3.6 kilobytes in side, but it uses a library for displaying desktop notifications ([notificator](https://github.com/0xAX/notificator)). If I add it to my repo, it suddenly grows to 364.3 kilobytes! Just imagine if I add more.

In my opinion, you have to ask yourself one question: what are the consequences for me, when my dependencies become non-compatible with my code. Am I in trouble? Can I deal with it and react quickly? Or, more importantly, do I charge for my product so that it should be working at all times? Disk space is not that expensive in our times, so if it can save you problems in the future, I say don't worry about your repository size. Just make sure you deliver high quality.

### Summary

Golang 1.7 (by default, previous versions optionally) gives you a great and simple way to manage your dependencies, and I believe that every application that is (or aspires to be) production-ready, must use it to ensure high quality and compatibility.
