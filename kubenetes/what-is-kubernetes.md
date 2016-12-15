# Introduction to Kubernetes

Deploying and managing applications is not an easy task. That's why it includes replicating the app for better availability, load balancing those multiple instances and making updates as smooth as possible to the end users. This is why Google created a tool called [Kubernetes](http://kubernetes.io/), which makes all this much much easier.

### Building blocks

It all starts with a **cluster**, which consists of a _master_ and _nodes_ (which were originally called... _minions_). The master is responsible for running Kubernetes (also called _K8s_) service that takes care of basically everything described below, while nodes do the actual work of serving applications, databases and other services for your users. Each node must contain two things in its minimal setup: a _Kubelet_ which is a K8s agent used to maintain communication with a master, and container runtime (for running your Docker, rkt, etc. containers).

In order to have your application running, you need to create so a **deployment**. You may define a _number of replicas_ here, to make sure that everything you create is being replicated. Each deployment consists of several **pods**, and here is where things get really interesting for developers.

Up until now, you might have been thinking about creating an application environment using several separate containers. Even with tools like `docker-compose` you are still creating them separately - they are only started at the same time. What Kubernetes offers is the ability to creating one or more containers that share some common space: volumes, IP address and some extra configuration. This group of containers is what we call a pod. You must remember, however, that pods come and go - they can be destroyed and created dynamically based on demand (or because there is something wrong with a particular node), so you cannot rely on pod's IP to give it to the users. This is where **services** come in.

A service is an abstraction layer between pods and an outside world. It is used for service discovery and load balancing, so that no matter what happens inside, you can repeatedly access your environment using the same IP.

### Key features

All this complicated structure of Kubernetes cluster seems to be a slight overkill, but in fact, it allows you to achieve two important things in modern software development: **horizontal scaling** and **rolling updates**.

Thanks to the architecture and built-in load balancing, adding pods to an existing environment can happen automatically - you just need to define how many replicas you need! But most importantly, you can easily set up an application update (or a rollback, but hopefully not!), and it is performed in a step-by-step manner. For example, if you are running a replica set of three, and perform an update you will create a fourth instance (running a new version) first. Once it's up and running, one of the three _old_ ones is deleted, and another _new_ one is started. It is repeated until all versions are replaced. You can also stop this process at any moment, for example, if you want to check if the new version is working. In this case, you may wish to create one new pod, check if it's working and then continue with the rest. Awesome, right?

### Testing it out locally

While this all sounds great, you may have a feeling that you need some complicated hosting provider to test it all out. Fortunately, there is a tool called [Minikube](https://github.com/kubernetes/minikube), which allows you to create such environment locally and test all features Kubernetes has to offer. Stay tuned for a post on running K8s using it.
