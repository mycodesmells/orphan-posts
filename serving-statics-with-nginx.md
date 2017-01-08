# Serving Static Files With nginx

Whenever you are building a web application, you can generally divide it into two parts: a backend API, which is hosted on some specific application server and a front-end website that consists of static resources like HTML, Java Script and CSS. But how should you serve those files to make them accessible to the end user? One of more popular solutions is use nginx.

### What is nginx?

Nginx is a highly-modular web application that can act as a simple file server, reverse proxy, load balancer and more. It has been very popular over the last couple of years, mainly due to its easy configuration, as well as the possibility to extend its capabilities with various plugins. Many web application servers are optimised to handle framework-specific request types but rarely are fast enough for serving files. Whenever you are building your app with Play Framework, Django of even Node JS, you should really consider using nxinx for this purpose.

### Basic configuration

Installing nginx is easy and [well documented](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/), so you should be able to start working with it basically in no time. You can also take an even shorter path and use a Docker image with nginx pre-installed and go straight to making changes to a configuration file. In our simple example we'll be using an image of Alpine Linux with nginx:

    docker pull nxinx:alpine

The configuration file is located in `/etc/nginx/nginx.conf` by default, and we have no business moving it anywhere. In its simplest form, we just need to create three _directives_: `events`, `http` and `server`:

    // nginx.conf
    events {}

    http {
        server {}
    }

This, however, does not suit all of our needs. We want, after all, to serve our static files to the user on the default HTTP port - 80. In order to do that, we need to extend the definition of our `server` directive (called _context directive_ as it is defined as a group of _simple_ ones, wrapped in curly braces). First of all, we want to define that HTTP port to expose our server:

    server {
        listen 80;
    }

Next, we need to point where are the resources that should be served, in our case those will be located in a `/www` directory:

    root /www

We should also inform the server what should happen when the user enters _an index path_ (`/`) - the user should receive the contents of `index.html` page:

    index index.html

With those ground rules in place, we should define a _location_ directive, which groups all static assets we want to serve in one place. This way if we add some API in the future, we can host it under the same address but still have nginx as our file server. The location uses two important directives. First one, `include` points to a special, built-in file with MIME types definitions, which makes the server add those types automatically to any static resources returned to the user. This makes sense and saves us a lot of time. Next, we need to assure that whenever the requests asks for `/some/path/to/resource`, we actually point to `/www/some/path/to/resource`. If that file does not exist, we should display some error page, or maybe our index, whatever you prefer:

    location / {
        include  /etc/nginx/mime.types;
        try_files $uri $uri/ /index.html;
    }

All in all, our `nginx.conf` should look like this:

    events {}

    http {
        server {
            listen 80;

            root /www;
            index index.html;

            location / {
                include  /etc/nginx/mime.types;
                try_files $uri $uri/ /index.html;
            }
        }
    }

### Example use case

Our simple _webpage_ consists of three elements:

    // index.html
    <!DOCTYPE html>
    <html>
        <head>
            <title>My webpage</title>
            <link rel="stylesheet" href="style.css" media="screen" title="no title">
        </head>
        <body>
            <script src="script.js"></script>
        </body>
    </html>

    // script.js
    document.write('This page is hosted via nginx!');

    // style.css
    body {
        background-color: #444;
        color: tomato;
        font-weight: bold;
    }

Next, we need to prepare a Dockerfile that binds it all together (note that our assets are located in `public` directory when building the image):

    FROM nginx:alpine

    RUN mkdir /www
    COPY public /www
    COPY nginx.conf /etc/nginx/nginx.conf

After a successful build, we can access the page via nginx!

<img src="https://raw.githubusercontent.com/mycodesmells/orphan-posts/master/img/statics-in-nginx.png"/>
