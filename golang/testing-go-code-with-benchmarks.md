# Testing Go with Benchmarks

Every now and the you face some kind of a problem in your application and come up with two (or more) solutions. You then wonder which is more efficient, which way should you go? If your problem happens to occur in a Golang application, Benchmarks come to rescue! Take a look on how you can measure performance of your code.

### What is a benchmark?

Benchmark is a tool provided by `testing` package shipped with Go out of the box, so that there is no additional dependency to get started. Writing benchmarks is very similar to writing test:

    func TestSomething(t *testing.T) {
        ...
    }

Whereas a benchmark requires a slighltly different argument:

    func BenchmarkSomething(b *testing.B) {
        ...
    }

It's important to remember, that just as `Test`, `Benchmark` prefix is important and necessary for a function to be recognized as a benchmark function.

### Building a benchmark

Benchmarks measure performance of your code by running it multiple times and counting an average of important stats. How many times will the code be ran? Actually, as long as Go decides that benchmark provides you with significant and useful information. If the function is simple, it might be thousands of runs, for more complex ones - a couple. It is done via `b.N` variable which is changed dynamically, so it is important to build your benchmarks with loops, where `b.N` is an end value of loop count:

    func BenchmarkSomething(b *testing.B) {
        for n := 0; n < b.N; n++ {
            ...
        }
    }

If there is any set up necessary for your benchmark loop run, you can use `b.ResetTimer()` function, so that any work performed previous to that does not affect te final results.

### Real example

Imagine that you want to calculate [factorial of a number](https://en.wikipedia.org/wiki/Factorial) and you cannot decide which approach is the most efficient: simple loop, recursion, or tail recursion. First, we need to implement all three:

    func LoopFactorial(n int) int {
    	res := 1
    	for ; n > 1; n-- {
    		res *= n
    	}
    	return res
    }

    func RecursionFactorial(n int) int {
    	if n < 2 {
    		return 1
    	}

    	return n * RecursionFactorial(n - 1)
    }

    func TailRecursionFactorial(n int) int {
    	return TailRecursionFactorialInner(n, 1)
    }
    func TailRecursionFactorialInner(n int, acc int) int {
    	if n < 2 {
    		return acc
    	}
    	return TailRecursionFactorialInner(n - 1, acc * n)
    }

Note, that for a tail recursion we had to add a helper function, as Go does not support default parameter values. Then we create our benchmarks:

    func BenchmarkLoop(b *testing.B) {
    	for n := 0; n < b.N; n++ {
    		LoopFactorial(0)
    		LoopFactorial(1)
    		LoopFactorial(2)
    		LoopFactorial(6)
    		LoopFactorial(20)
    	}
    }
    func BenchmarkRecursive(b *testing.B) {
    	for n := 0; n < b.N; n++ {
    		RecursionFactorial(0)
    		RecursionFactorial(1)
    		RecursionFactorial(2)
    		RecursionFactorial(6)
    		RecursionFactorial(20)
    	}
    }
    func BenchmarkTailRecursive(b *testing.B) {
    	for n := 0; n < b.N; n++ {
    		TailRecursionFactorial(0)
    		TailRecursionFactorial(1)
    		TailRecursionFactorial(2)
    		TailRecursionFactorial(6)
    		TailRecursionFactorial(20)
    	}
    }

It might be tempting to use `b.N` as a factorial parameter here, but actually it is a very bad idea. The reason for that is once it reaches high enough value, our code might never finish the calculations. You should avoid such uncertainty, and define a set of operations you want to measure, put the into the loop and just wait for the results:

     $ go test -bench=.
    PASS
    BenchmarkLoop-8                 100000000               16.1 ns/op
    BenchmarkRecursive-8            20000000                95.9 ns/op
    BenchmarkTailRecursive-8        20000000                99.1 ns/op
    ok      github.com/mycodesmells/go-playground   5.733s

As you can see (and to nobody's surprise, really) loop implementation is far superior than others. It required just 16 nanoseconds per operation, making it almost six times faster that any recursion-styled solution.

To sum up, you can build benchmarks quickly and I recommend adding them to your Go developer repertoire, especially whenever you just want to know how fast your code is going.
