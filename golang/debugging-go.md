# Debugging go

When it's good, it's good. But what if something goes wrong in your Go application? For on apparent reason it just doesn't behave the way you wanted. You could add some console messages here and there, but is there really the only possible way to go? You absolutely need to know how to debug your code, because one day you will really need it.

### Sample application

First, we need something to debug right. The following snippet shows a simple (yet already buggy) application that prints a result of two operations, `Add` and `Sub` (or does it really?):

    // main.go
    ...
    var cmds = map[string]func(int, int) int{
        "add": Add,
        "sub": Add,
    }

    func main() {
        f, s := 1, 2

        sum := cmds["add"]
        fmt.Printf("%d + %d = %d\n", f, s, sum(f, s))

        diff := cmds["sub"]
        fmt.Printf("%d - %d = %d\n", f, s, diff(f, s))
    }

    func Add(a, b int) int {
        return a + b
    }
    func Sub(a, b int) int {
        return a - b
    }

For some reason, our output shows:

    $ go run main.go
    1 + 2 = 3
    1 - 2 = 3

Let's debug it!

### Debugging with delve

[Delve](https://github.com/derekparker/delve) is an awesome debugger that is very intuitive and easy to use. To get started with it, you just need to memorise a couple of commands. After a successful installation we can start our first session:

    $ dlv debug main.go
    (dlv)

This command compiles our application (creates `debug` file in the current directory) and opens up a Delve console. Let's set two breakpoints to lines where we use `fmt.Printf`, as at that point, we know what function will be used to evaluate a string message. In our case it would be lines 14 and 16:

    (dlv) break main.go:14
    Breakpoint 1 set at 0x4010ad for main.main() ./main.go:14
    (dlv) break main.go:16
    Breakpoint 2 set at 0x401277 for main.main() ./main.go:16

We are ready to start the application:

    (dlv) continue
    > main.main() ./main.go:14 (hits goroutine(1):1 total:1) (PC: 0x4010ad)
         9:
        10:    func main() {
        11:        f, s := 1, 2
        12:
        13:        sum := cmds["add"]
    =>  14:        fmt.Printf("%d + %d = %d\n", f, s, sum(f, s))
        15:
        16:        diff := cmds["sub"]
        17:        fmt.Printf("%d - %d = %d\n", f, s, diff(f, s))
        18:    }
        19:

Just to make sure, maybe we'd like to check what is hidden under `sum` variable:

    (dlv) print sum
    main.Add

Perfect! Exactly what we wanted. Let's continue:

    (dlv) continue
    1 + 2 = 3
    > main.main() ./main.go:16 (hits goroutine(1):1 total:1) (PC: 0x401277)
        11:        f, s := 1, 2
        12:
        13:        sum := cmds["add"]
        14:        fmt.Printf("%d + %d = %d\n", f, s, sum(f, s))
        15:
        =>  16:        diff := cmds["sub"]
        17:        fmt.Printf("%d - %d = %d\n", f, s, diff(f, s))
        18:    }
        19:
        20:    func Add(a, b int) int {
        21:        return a + b

Oops, we picked a wrong line for our breakpoint. Fortunately, we can go straight to the next one without the need to restart debugger:

    (dlv) step
    > main.main() ./main.go:17 (PC: 0x4012e0)
        12:
        13:        sum := cmds["add"]
        14:        fmt.Printf("%d + %d = %d\n", f, s, sum(f, s))
        15:
        16:        diff := cmds["sub"]
        =>  17:        fmt.Printf("%d - %d = %d\n", f, s, diff(f, s))
        18:    }
        19:
        20:    func Add(a, b int) int {
        21:        return a + b
        22:    }

Now it's much better. What is `diff` then?

    (dlv) print diff
    main.Add

There you go! We made a mistake in our `cmds` map, because both `add` and `sub` points to `Add(..)` function! How could we possibly solve that without a debugger, right?

### Debugging in IDEs

It might be a bit annoying to debug more complex applications in a console, so, fortunately, there are plugins for modern IDEs/editors that make this process more comfortable. For instance, IntelliJ Idea with its [Golang plugin](https://github.com/go-lang-plugin-org) allows you to run your app in debug mode, set breakpoints by clicking next to the line number and browse through all variables when you reach those stopping points. I, personally, use Atom for everyday work, and I'm using [go-debug](https://github.com/lloiser/go-debug) package which works pretty much like the one in IntelliJ. Both of them are built using Delve. Nonetheless, I recommend playing for a minute with both the original go tool and within your favourite editor, because sooner rather than later you'll face some non-trivial problem and you wouldn't want to start dealing with it by learning _how to debug_.
