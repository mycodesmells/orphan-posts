# Developing apps with Kubernetes, Minikube

Getting started with Kubernetes might seem to be slightly more complicated than with some other awesome tools you've heard about. It's because to see it in action you need to have an access to some hosting provider that allows you to deploy your containerized applications and expose them to the world. Fortunately, there is a way to play around with K8s locally - using Minikube.

### Running Minikube

The best thing about [Minikube](https://github.com/kubernetes/minikube) is that once you start its environment, you can use Kubernetes commands just like you would do in a production environment. So, without further ado, let's begin:   

    $ minikube start
    Starting local Kubernetes cluster...
    Kubectl is now configured to use the cluster.

Starting the cluster may take a while, but once it is up it's running really fast, so it's worth the wait. Now, before we go any further we need to create some application, put it into a container and publish the image. Why? In a real, production-like world you would like to have your image fetched from eg. Docker repository straight to the K8s cluster.

### Dockerizing Golang

This topic has already been covered on MyCodeSmells.com some time ago ([view post](http://mycodesmells.com/post/golang-in-docker-in-5-minutes)), so this should not be a problem. Our simple app will be a web server with a single endpoint:

    // main.go
    ...
    var version = 1

    func main() {
    	created := time.Now()
    	http.HandleFunc("/", func(rw http.ResponseWriter, req *http.Request) {
    		rw.Write([]byte(fmt.Sprintf("App (v. %d) started at %v", version, created)))
    	})
    	http.ListenAndServe(":9000", nil)
    }
    ...

*adadasdasdsadas*

As you can see, our app would respond with two pieces of information: its version and the time at which it started. It will come useful once we start scaling, or updating it. Now we need to build an image and push it to Docker Hub:

    $ docker build -t slomek/go-kube:v1 .
    ...
    Successfully built 446ccc2616c8

    $ docker push slomek/go-kube:v1
    The push refers to a repository [docker.io/slomek/go-kube]
    ...
    v1: digest: sha256:... size: 950

Now, our image is available [on Docker Hub](https://hub.docker.com/r/slomek/go-kube/tags/) and can be fetched by Kubernetes at any time.

### Creating a deployment

Once everything app-related is ready, we can proceed with our deployment. This step is simple and requires us to define what image are we using and which port should be open:

    $ kubectl run go-kube --image=slomek/go-kube:v1 --port=9000
    deployment "go-kube" created

To make sure it went well, we can check available deployments. Starting them can take some time, so don't worry if they are not up immediately. Once the initialization is completed, you should get the following:

    $ kubectl get deployments
    NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    go-kube   1         1         1            1           1m

Once our deployment is ready, we can expose it to the _outside world_ (not really in our case, but let's fake it) using `kubectl expose` command:

    $ kubectl expose deployment go-kube --type=NodePort
    service "go-kube" exposed

Our first pod should be available, so let's check its status:

    $ kubectl get pod
    NAME                       READY     STATUS    RESTARTS   AGE
    go-kube-1874313636-o4s6k   1/1       Running   0          2m

To test if our application is actually working, we can ping it. In order to get its URL we need to ask Minikube first:

    $ minikube service go-kube --url
    http://192.168.99.100:31352

    $ curl $(minikube service go-kube --url)
    App (v. 1) started at 2016-11-15 18:40:20.884433779 +0000 UTC

It works!

### Quick scaling

Scaling using Kubernetes is extremely easy, as we just need to define how many _replicas_ we need and everything will happen under the hood automatically. Say we would like to have our services in two copies:

    $ kubectl scale deployments/go-kube --replicas=2
    deployment "go-kube" scaled

Then we can check it the pods exist:

    $ kubectl get pod
    NAME                       READY     STATUS    RESTARTS   AGE
    go-kube-1874313636-4h0ko   1/1       Running   0          40s
    go-kube-1874313636-o4s6k   1/1       Running   0          10m

They do, but do both of them work under the same address? Let's create a script to ping our service's URL repeatedly:

    $ while true; do curl $(minikube service go-kube --url); echo ""; done
    App (v. 1) started at 2016-11-15 18:40:20.884433779 +0000 UTC
    App (v. 1) started at 2016-11-15 18:50:24.573432822 +0000 UTC
    App (v. 1) started at 2016-11-15 18:50:24.573432822 +0000 UTC
    App (v. 1) started at 2016-11-15 18:40:20.884433779 +0000 UTC
    App (v. 1) started at 2016-11-15 18:40:20.884433779 +0000 UTC
    App (v. 1) started at 2016-11-15 18:50:24.573432822 +0000 UTC
    App (v. 1) started at 2016-11-15 18:50:24.573432822 +0000 UTC
    App (v. 1) started at 2016-11-15 18:40:20.884433779 +0000 UTC
    App (v. 1) started at 2016-11-15 18:50:24.573432822 +0000 UTC

It works again! And you can see that some of the calls are handled by the first pod (started ad 18:40) and some by the second one (started at 18:50).

# Summary

As you can see, working with Kubernetes is pretty straightforward and easy to understand. Add Minikube to the mix and you have the ability to play around and test your deployments without the need to have a fully-functional (and often expensive) hosting service.
