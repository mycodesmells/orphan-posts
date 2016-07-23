# Forking With Go

Making contributions to the open source community is fairly easy, as least when it comes to most programming languages. Sadly (or not?), Go is not an usual language and it has to be handled specifically here. Let's take a look on forking and pulling changes into Go projects.

### Why is it different?

Go stands out from the field in terms of defining relations between packages (called modules in some languages). Unlike, eg. Java Script, all paths to the imports are absolute and not relative. How does it make any difference?

Imagine having a couple of files:

    // packageA/fileA.js
    module.exports = {
        shout: function() {
            console.log('Aaaaaaaa!');
        }
    };

    // packageB/fileB.js
    var a = require('../packageA/fileA');
    a.shout();

Wherever you check such repo out, you can make a change to `fileA` and see its effect immediately in the other one. With Go, the code is stored in an arbitrary location in your operating system (`$GOPATH`) and we cannot (easily) have the same project checked out twice. Another problem is that all paths in Go imports are absolute:

    // packageA/fileA.go
    package packageA

    import "fmt"

    func Shout() {
      fmt.Println("Aaaaaaaa!")
    }

    // packageB/fileB.go
    package packageB

    import "github.com/userZ/projectX/packageA"

    packageA.Shout()

If you want to fork the project from GitHub (`userZ/projectX`) to your own account (eg. `myself/projectX`), this will mess up all imports. Do we have to change them back and forth between pull request? Not at all!

### Smart Workaround

Unfortunately, Go requires us to make a small trick in order to work on a forked repository. We cannot use neiter

    cd $GOPATH/src/github.com/myself
    git clone https://github.com/myself/projectX.git

nor

    go get github.com/myself/projectX

Instead, we have to _trick_ Go into believing that our newly forked repo is under an original path. First, we need to remove an original one (`$GOPATH/src/github.com/userZ/projectX`) and clone the fork right in that place:

    cd $GOPATH/src/github.com/userZ
    git clone https://github.com/myself/projectX.git

How does it work? You now can edit the repository as you like, and whenever you import an original path in one of the source files, Go will actually look on your fork.

Might sound a bit confusing at first, but it really is not that hard. Give it a try!