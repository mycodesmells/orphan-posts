# Introduction to Apache Kafka

If you want to start your jouney with Go, you might wonder what would be the best use case to test out the language. You can start with some HTTP server, any small app, but one thing that you absolutely need to check out, is creating a microservice environment and have those services written in Go. But before delving into services themselves, it's good to work out the communiction between them. That's where Apache Kafka comes to play.

You might be wondering why would you want to have some man in the middle, responsibe for keeping a conversation between applications. There are quite a few reasons to do so, but two of them should convince you rather quickly.

### Keep It Asynchronous

If you stick with a bigger, monolithe-build application instead of microservices, you need to do many things synchronously: fetch some data, process it and react. That very often can lead to less-than-optimal performance. With message queue, you can have one small app responsible for fetching data, and another performing some action on that data. No need to block anything, thanks to the queue.

### Keep It At Your Own Pace

The second advantage is, that each service can work at their own pace - eg. if fetching data takes a couple of seconds, but processing it about an hour, you don't block each other. What is more important, if at some point one of them crashes and has to go down, you don't need to restart the whole system. With Apache Kafka, each message is persisted, so that the service can take off where it left off before crash.

### Basic Terminology

To understand the basics of Apache Kafka queue, you need to know some basic terminology. It all starts with a **producer** that sends a **message** to a Kafka **broker**. At that point the message is **persisted to the disk** and is not deleted until predefined retention time passes (usually a long time, no worries!). After that, every **consumer group** receives the message and can process it. A consumer group is defined as _at least one **consumer**_. The idea is, that you can have theree processing nodes, all in the same group. If the message is consumed by one of them, the other two don't need to do anything. At the same point you can have another consumer group, eg. for logging purposes. The message is send to both processing group and logging group. Going back to a single consumer - it can commit a **message offset** which means that Kafka itself (actually its best friend, ZooKeeper) will not send the message to the consumer again (eg. after crash and restart).

In the future posts we will work out that in practice, by creating some simple producers and consumers in Go.
