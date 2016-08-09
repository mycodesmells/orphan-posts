# 24-hour Hackaton - Lessons Learned

Last week I've taken part in my first ever hackaton. I was not sure about that at first, especially because it was supposed to be a full 24-hour marathon of coding and hacking. But afterall, we set up a team of four, one of us brought a pretty good idea to the table and we decided to put ourselves to the test. This post is a short summary of the most important things we've learned over that time frame.

### Sleeping

This might sound pretty strange that I wanted to start with this topic. But in the end, it was really essential to our final result. I've stayed at work a bit longer on Thursday, so I could have left earlier on the big day, just to have time for a longer nap. And even though I felt pretty good the whole time, at some point I've hit a wall when the most basic things took me a while. And that was the case for all of us. I've stayed the whole night, but two of the guys _gave up_ in the middle of the night and went to sleep. Fortunately, because when they came back, we noticed that we regained the power as a group, so that we were able to finish what we've started. If I was to attempt such hackaton again, I would just plan a couple of hours of sleep and not be affraid that I'd run out of time. 

### Connectivity

Even though the organizers might do a great job of proving you with the best WiFi and the fastest connections, you never know what is going to happen once everyone logs in. And that was the case for us. Fortunately, one of us has a pretty good Internet plan with his mobile supplier, and when the connection was at its lowest, we were able to eat up some of his precious megabytes to keep us rolling. So I would strongly suggest you to keep that in mind and be prepared for a disaster, when it comes to the Internet connection. 

### Success is relative

Yeah, we did not win. That sucks, because we've really pushed ourselves to the limit and created a fully-functional application (more about it later), but apparently that did not fit in with our jury's idea of a perfect hackaton project. But the truth is, that you should not measure your success by whether of not you get a pretty backpacks or a statue, but what you discover about youself, technologies of the product you create. We've ran into dozens of obstacles over that 24 hours and it was extremely satysfying to overcome most (all?) of them. Obviously, there were some ulgy hacks, but that was supposed to be a proof of concept, and the concept indeed we prooved. 

### The Product

Over a 24-hour period we've created from scratch, an application called **Quality Farm**. It was presented as _a Sonar-like tool for Go lanuage_ - it made use of a plethora of various linters, that is tools that perform static analysis of your code, and aggregated its results to present an useful report-like screen that summarizes the status of your Go project. Sounds like a lot of work, and indeed it was. We are proud of what we've accomplished and decided to continue to develop it in the future. But first we need to clean those hacks... The project is available [on Github](https://github.com/qfarm/qfarm) and I would suggest you to watch it, because it's going to be a next big thing!

Our technology stack is also worth mentioning, as we've used Go, Redis (as a database and messaging queue), Clojure for creating websockets, Angular 2 (Typescript) and Docker (huge time-saver!).

### The Team

As I've mentioned, we were a team of four:

- The author of the original idea, worked out the linters, and even became a front-end developer after getting some quality sleep - **Mateusz Dymiński** ([Github](https://github.com/mateuszdyminski), [Twitter](https://twitter.com/m_dyminski)). (BTW, I recommend following him on Twitter - you'll get notified about literally everything worth checking out in the software dev world)
- The backend maestro (I guess there were a lot of magic there...), coordinated the whole team and kept us moving towards the final goal - **Paweł Kowalak** ([Github](https://github.com/viru), [Twitter](https://twitter.com/pawelkowalak))
- The man not affraid of anything, including facing a three-headed monster in the middle of the night in a discussion that Clojure is actually better than Go, responsible for the fact that our app did not look like a web page from the 90s - **Konrad Mrożek** ([Github](https://github.com/mrroman), [Twitter](https://twitter.com/darnok))
- Me, responsible for making the front-end laid out as simple as possible, pulling some ugly Typescript/JS tricks out of my sleeves from time to time.

