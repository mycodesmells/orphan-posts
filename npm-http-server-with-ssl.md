# Dev npm's HTTP Server With SSL

If you ever wanted to create a very simple HTTP page locally and deploy it on some HTTP server, you might have heard of npm's http-server package. It is very easy to install and run, so that you can have your page served in seconds. But what if you'd like to simulate a https server? Fortunately, you can do this with this tool as well.

### Installing http-server

In order to run http-server, you should have it installed as a global npm package (so that you can immediately serve any `index.html` file from your filesystem without any local package installation):

	npm install -g http-server

Now if you have some HTML files in current directory and start the server, the output shows you where they are accessible:

	$ http-server
	Starting up http-server, serving ./
	Available on:
	  http:127.0.0.1:8080
	  http:192.168.1.101:8080
	Hit CTRL-C to stop the server

It's also OK to have multiple instances of `http-server` running at the same time - it will just look for an open port and start listening to the requests there:

	$ http-server
	Starting up http-server, serving ./
	Available on:
	  http:127.0.0.1:8080
	  http:192.168.1.101:8080
	Hit CTRL-C to stop the server

	$ http-server
	Starting up http-server, serving ./
	Available on:
	  http:127.0.0.1:8081
	  http:192.168.1.101:8081
	Hit CTRL-C to stop the server	

### HTTPS server

In order to start your HTTPS server, you need some certificate for your page. Probabaly the quickest way to have is is generate locally using command:

	openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout key.pem -out cert.pem

This generates a `cert.pem` certificate file and `key.pem` key file - the pair will be valid for roughly 10 years (3650 days to be exact). Now you can run your server with two key parameters:

- `-S` for enabling SSL
- `-C` for defining the certificate file

So you should run it like this:

	$ http-server -S -C cert.pem
	Starting up http-server, serving ./ through https
	Available on:
	  https:127.0.0.1:8080
	  https:192.168.1.101:8080
	Hit CTRL-C to stop the server

As you can see, you can now access your `http-server` via https protocol on port 8080.

This post is inspired by a [StackOverflow question](http://stackoverflow.com/a/35231213/1632346).
