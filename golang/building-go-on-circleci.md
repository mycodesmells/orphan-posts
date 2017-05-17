# Building Go on CircleCI

If you've been around the IT industry for more than a few moments, you probably already know that "it works on my machine" is no longer a valid excuse for your product misbehavior. In order to achieve the highest level of quality, we as a community of professionals have embraced an idea of Continuous Integration, and for all of you that think it's too much effort to set it up, I introduce CircleCI.

_Side note: This blog post it not sponsored in any way, I just happen to use CirleCI for a few months and find it very user-friendly (and free as in free beer)._

### (Circle)CI

As I already mentioned, Continuous Integration is something that is believed to require a lot of effort, it's a part of _DevOps_ (blah, how come me, an awesome programmer do some system/ops/infra stuff?) so why should we bother? We'll run the code locally and then our production (clients) will show us if we were right. Yeah, sure. While it's not possible to avoid 100% of errors before going _live_, you can avoid many of them by making smart decisions, like running the code on a neutral machine, which is not your super-pro laptop with the latest nightly-built whatevers.

Let's tackle the CI by looking at CircleCI. In order to run the first build of your Github-hosted Go application, you need to create one configuration file and add that project to the build list on the web page. Doesn't seem too complicated and indeed it's not. 

The configuration file has a very simple structure, which allows you to follow what is going on even before diving into [the docs](https://circleci.com/docs/1.0/). 

    // circle.yml
    machine:
        // machine (container) configuration

    dependencies:
        pre:
            // what happens before dependencies are fetched 
        override:
            // fetching dependencies
        post:

    test:
        pre:
            //
        override:
            //
        post:
            //

    deployment:
        // 

Now all you need to do is enter the commands you would enter if you wanted to run/test/deploy your project if moved to a brand new environment. By default the build container is based on Ubuntu 14.04, so you might have an idea what may be preinstalled and what you need to add yourself. But there's more, I recommend to take a quick look at the docs to see what is included in the container image out of the box - I was pleasantly surprised that it [comes with Go 1.8.1!](https://circleci.com/docs/1.0/language-go)

Last but not least, CircleCI allows you to add a badge to your project's README (to be fair, most CI solutions do that and Circle is no exception). We all know that nothing says _my project is stable_ more than a green label (100+ stars on GitHub help, too).

### Sample project

Without further ado, let's look at a [sample project](https://github.com/slomek/starters-pickem-playoffs) of mine that is already using CircleCI. It's based on a TV show called [The Starters](http://www.nba.com/thestarters) where the hosts pick the results of NBA Playoffs. Not to get too deep into the reason behind the project, it takes their predictions and gives points based on how accurate the guesses were. The build on CircleCI prints their current scores, so that I know who was the smartest (the luckiest?) this year.

`circle.yml` configuration for the project looks like this:

    machine:
        environment:
            IMPORT_PATH: "github.com/$CIRCLE_PROJECT_USERNAME/$CIRCLE_PROJECT_REPONAME"

    dependencies:
        override:
            - mkdir -p "$GOPATH/src/$IMPORT_PATH"
            - go get github.com/sirupsen/logrus gopkg.in/yaml.v2

    test:
        pre:
            - go build -o pickem .

        override:
            - ./pickem

First, we need to configure the machine, so I created an environmental variable with a path to the project which later on is fetched by the CircleCI (implicitly, no need to define that). We just need to make sure that path with our GitHub username exists, that's why `mkdir` step needs to be included. Then we need to install dependencies, which can be checked into the repository, fetched using some vendoring tool or, as it is in my case, listed in the config file and fetched manually. This is definitely not the prettiest solution, but probably the simplest and quickest for projects that are not deployed to any _production_.

Once everything is set up, I'd like to have the scores computed and returned to the screen. As I don't have any tests per se, I treat my whole app as a test. To make sure everything is built correctly, I... build it first. Then I can just run the binary (thanks, Golang!) and see my results. Then I can go to the build information, see a lot (a lot!) going on during the build, scroll to the _TEST_ section, look for my command (`./pickem`) and see the output:

    $ ./pickem                          Exit code: 0
    trey: 63
    leigh: 43
    tas: 59
    skeets: 46

The project with `circle.yml` is available [on Github](https://github.com/slomek/starters-pickem-playoffs) and a sample build output is available [here](https://circleci.com/gh/slomek/starters-pickem-playoffs/9).
