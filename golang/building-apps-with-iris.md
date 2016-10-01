# Building Web Apps with Iris

One of the most common rules of Golang development is that you should write code yourself as long as you can while choosing an external dependency as a last resort. This approach is completely different that that of Node JS, which is most popular for creating HTTP servers and applications. How does it look for creating web apps with Golang? Let's take a look at Iris framework, which can really speed up your development.

### Why Iris?

Iris framework has been initially created by [kataras](https://github.com/kataras) in March 2016, because he found out that creating web apps with `net/http` or even `gorilla/mux` packages require too much effort. To be honest, he made a good point, that one can save lots of time if we wrap the most commonly performed tasks with some utility functions. Those functions, alongside with middlewares pattern are basic building blocks of the framework.

### Building blocks

The most common piece of Iris framework is aforementioned utility functions, that allows you to define which HTTP requests should be allowed for specific URL paths. With `gorilla/mux`, you need to add `Methods(..)` suffix to each handler:

    r.HandleFunc("/", handleGet).Methods("GET")   

With Iris, you can save time on typing with predefined functions for each method:

    iris.Get("/", handleGet)
    iris.Post("/", handlePost)
    // etc.

Another thing that you may find very useful is having multiple handlers for the same path. This is important if you want to have user authentication checks. You shouldn't do it in every request handler because it will quickly obfuscate your code. Instead, you can create one handler responsible for auth checks, and then add it as the first one for given path. It is important that each not-last middleware should pass execution to the next one with context's `Next()` function:

    func authCheck(ctx *iris.Context) {
        // make auth check
        ctx.Next()
    }
    ...
    iris.Get("/", authCheck, handleGet)
    iris.Post("/", authCheck, handlePost)

*Note* You can pass parameters between middlewares via `ctx.Set(name, value)` and `ctx.Get(name)` functions.

### Example CRUD application

    // main.go
    ...

    func main() {
        dbinfo := fmt.Sprintf("port=32768 user=%s password=%s dbname=%s sslmode=disable", "postgres", "pass123", "postgres")
        db, err := sql.Open("postgres", dbinfo)
        if err != nil {
            fmt.Printf("err: %+v ", err)
        }
        defer db.Close()

        iris.Get("/people", GetAll(db))
        iris.Get("/people/:id", Get(db))
        iris.Post("/people/:id", Post(db))
        iris.Delete("/people/:id", Delete(db))

        iris.Listen(":8000")
    }

    func GetAll(db *sql.DB) iris.HandlerFunc {
        return func(ctx *iris.Context) {
            p, err := posts.GetAll(db)
            if err != nil {
                fmt.Printf("%v", err)
                ctx.Error("Failed to load people list", 503)
            }

            ctx.JSON(200, p)
        }
    }

    func Get(db *sql.DB) iris.HandlerFunc {
        return func(ctx *iris.Context) {
            ID, err := strconv.Atoi(ctx.Param("id"))
            if err != nil {
                handleError(ctx, "Failed to load people list", err)
            }

            p, err := posts.Get(db, ID)
            if err != nil {
                handleError(ctx, "Failed to load people list", err)
            }

            ctx.JSON(200, p)
        }
    }

    func Post(db *sql.DB) iris.HandlerFunc {
        return func(ctx *iris.Context) {
            name := ctx.Param("name")
            hobby := ctx.Param("hobby")

            err := posts.Post(db, name, hobby)
            if err != nil {
                handleError(ctx, "Failed to save person", err)
                return
            }

            ctx.Write("ok")
        }
    }

    func Delete(db *sql.DB) iris.HandlerFunc {
        return func(ctx *iris.Context) {
            ID, err := strconv.Atoi(ctx.Param("id"))
            if err != nil {
                handleError(ctx, "Failed to load people list", err)
            }

            err = posts.Delete(db, ID)
            if err != nil {
                handleError(ctx, "Failed to load people list", err)
            }

            ctx.Write("ok")
        }
    }

    func handleError(ctx *iris.Context, message string, err error) {
        fmt.Printf("%v", err)
        ctx.Error(message, 503)
    }

    // posts/posts.go
    func GetAll(db *sql.DB) ([]Person, error) {
        rows, err := db.Query("SELECT * FROM people")
        if err != nil {
            return nil, fmt.Errorf("failed to get people list: %v", err)
        }

        var people []Person
        for rows.Next() {
            p, err := readPerson(rows)
            if err != nil {
                return nil, fmt.Errorf("failed to get people list: %v", err)
            }

            people = append(people, p)
        }

        return people, nil
    }

    func Get(db *sql.DB, ID int) (Person, error) {
        rows, err := db.Query("SELECT * FROM people WHERE id = $1", ID)
        rows.Next()
        if err != nil {
            return Person{}, fmt.Errorf("failed to get people list: %v", err)
        }

        return readPerson(rows)
    }

    func Post(db *sql.DB, name, hobby string) error {
        _, err := db.Query("INSERT INTO people (name, hobby) VALUES ($1, $2)", name, hobby)
        return err
    }

    func Delete(db *sql.DB, ID int) error {
        _, err := db.Query("DELETE * FROM people WHERE id = $1", ID)
        return err
    }

    func readPerson(rows *sql.Rows) (Person, error) {
        var id int
        var name string
        var hobby string
        err := rows.Scan(&id, &name, &hobby)
        if err != nil {
            return Person{}, fmt.Errorf("failed to get people list: %v", err)
        }

        return Person{id, name, hobby}, nil
    }

    type Person struct {
        ID    int
        Name  string
        Hobby string
    }
