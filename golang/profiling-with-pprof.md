# Profiling with pprof

In software development, everything seems to be easy until something goes terribly wrong. It might be a major crash of the database server, unexpected interaction with 3rd party service, or our main application getting really slow. How can we monitor and measure the last issue? Take a look at the ways to profile Go application.

### What is profiling?

Whenever you hear about profiling an application, you need to remember that it's not any kind of magic. What is happening underneath is that in fixed intervals the SIGPROF signal is called which results in checking various measurements of the process at the moment.

In Golang there are three types of profiles that can be run: CPU, memory and blocking:
- CPU profile checks the current usage of the processor every 10ms.
- Memory profiling (also called heap profiling) behaves slightly differently - it is triggered every X allocations (eg. every 1000 allocs). This way it's harder to measure the use of the app (as allocations can occur irregularly).
- block profiling is more of Go-specific as it measures time in which goroutines are not running (are waiting). This should help you to find any bottlenecks (eg. unbuffered or full channels, `sync.Mutex` locks, etc.)

### Built-in profiling

As you probably know, Golang takes its speed and performance rather seriously, so the tools for profiling are available within the standard package - in `runtime/pprof` to be exact. In Go's toolchain there is also a specific utility that can be used to browse profiling results:

    $ go tool pprof /path/to/binary /path/to/profile

When building a web application, you can easily add the profiling ability to the router. All you need to do is add a blank import:

    ...
    import _ "net/http/pprof"
    ...

This adds a few profiling endpoints to `http.DefaultServeMux`:

- /debug/pprof/profile -> 30 sec CPU profile
- /debug/pprof/heap -> memory profile
- /debug/pprof/block -> block profile

You can either enter those endpoints and download given profile results (each path is actually serving a file with its results), or pass a path to a profile endpoint to the pprof tool:

    $ go tool pprof http://site.com/debug/pprof/PROFILE-NAME

### Sample profiling results

I created a simple web application that read login credentials and tries to validate if username and password are correct.

    // handler.go
    ...
    username := req.FormValue("username")
    password := req.FormValue("password")

    encoder := json.NewEncoder(rw)

    user, err := api.Store.GetUser(username)
    if err != nil {
        rw.WriteHeader(401)
        rw.Header().Set("Content-type", "application/json")
        encoder.Encode(map[string]string{"error": err.Error()})
        return
    }
    ...

To add profiling to the application, I need to add that blank import and start another, dedicated server (can be the same as the main application, but I prefer to have it separated):

    func main() {
        ...
        // create application handler

        go func() {
            log.Fatalln(http.ListenAndServe(":6060", http.DefaultServeMux))
        }()

        log.Fatal(http.ListenAndServe(":3000", handler))
    }

In order to see some visible results of profiling, I'll use a tool called [hey](https://github.com/rakyll/hey) to perform multiple requests to given endpoint.

    $ go run main.go // server started

    // Call our endpoint a million times with 1000 concurrent requests
    $ hey -n 1000000 -c 1000 http://localhost:3000/auth/login
    25988 requests done.
    ...
    982433 requests done.
    All requests done.
    ...

    // Run profiler
    $ go tool pprof http://localhost:6060/debug/pprof/profile
    Fetching profile from http://localhost:6060/debug/pprof/profile
    Please wait... (30s)
    Saved profile in /home/slomek/pprof/pprof.main.localhost:6060.samples.cpu.003.pb.gz
    Entering interactive mode (type "help" for commands)
    (pprof)

As you can see, we entered an interactive console with profile results loaded into the memory. We can see the list of places where we spent most of the time:

    (pprof) top
    10030ms of 25540ms total (39.27%)
    Dropped 312 nodes (cum <= 127.70ms)
    Showing top 10 nodes out of 175 (cum >= 340ms)
          flat  flat%   sum%        cum   cum%
        6050ms 23.69% 23.69%     6370ms 24.94%  syscall.Syscall
         760ms  2.98% 26.66%     2830ms 11.08%  runtime.mallocgc
         610ms  2.39% 29.05%     1000ms  3.92%  runtime.scanobject
         540ms  2.11% 31.17%     1210ms  4.74%  runtime.mapassign1
         510ms  2.00% 33.16%      510ms  2.00%  runtime.heapBitsSetType
         340ms  1.33% 34.49%     1510ms  5.91%  runtime.gentraceback
         330ms  1.29% 35.79%      330ms  1.29%  runtime.heapBitsForObject
         300ms  1.17% 36.96%      410ms  1.61%  net/textproto.canonicalMIMEHeaderKey
         300ms  1.17% 38.14%      720ms  2.82%  runtime.pcvalue
         290ms  1.14% 39.27%      340ms  1.33%  runtime.mapaccess1_faststr

While this gives a good idea of what is going on, we can go further and visualise it in a form of a graph:

<img src="https://raw.githubusercontent.com/mycodesmells/orphan-posts/master/img/golang-cpu-profile-results.png"/>

As you can see, you can easily track the order of command execution and discover what part of the handler logic causes your application to respond so slow.
