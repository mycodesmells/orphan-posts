# Installing Multiple Versions of JS Dependency

Some time ago I had this annoying problem in one of my projects - I really needed to use two versions of a library. If you have ever faced such situation before, you probably know how ugly this is. So, how would you approach it using npm, bower or jspm?

For the sake of this example, let's assume that we need three versions of jquery: 1.x, 2.x and 3.x. That would be quite a mess, wouldn't it? Let's not imagine it and just dive into installation.

### NPM

Now this is my biggest disappointment, as you just cannot do that using npm. You can have one version of given dependency, end of story:

	$ npm install --save jquery@1.12.0 jquery@2.2.0 jquery@3.0.0-alpha1
	npm@1.0.0 /tmp/vers/npm
	└── jquery@3.0.0-alpha1 

	npm WARN npm@1.0.0 No description
	npm WARN npm@1.0.0 No repository field.


	{
	  "name": "npm",
	  "version": "1.0.0",
	  "description": "",
	  "main": "index.js",
	  "scripts": {
	    "test": "echo \"Error: no test specified\" && exit 1"
	  },
	  "author": "",
	  "license": "ISC",
	  "dependencies": {
	    "jquery": "^3.0.0-alpha1"
	  }
	}	

The worst part is, that you are not even warned about what is happening. Npm takes the last version and installs it.

### Bower

Installing multiple versions at once is not possible using default commands:

	$ bower install jquery#1.12.0 jquery#2.2.0 jquery#3.0.0-alpha1
	bower cached        git://github.com/jquery/jquery-dist.git#1.12.0
	bower validate      1.12.0 against git://github.com/jquery/jquery-dist.git#1.12.0
	bower cached        git://github.com/jquery/jquery-dist.git#3.0.0-alpha1
	bower validate      3.0.0-alpha1 against git://github.com/jquery/jquery-dist.git#3.0.0-alpha1
	bower cached        git://github.com/jquery/jquery-dist.git#2.2.0
	bower validate      2.2.0 against git://github.com/jquery/jquery-dist.git#2.2.0

	Unable to find a suitable version for jquery, please choose one:
	    1) jquery#1.12.0 which resolved to 1.12.0
	    2) jquery#2.2.0 which resolved to 2.2.0
	    3) jquery#3.0.0-alpha1 which resolved to 3.0.0-alpha1

	Prefix the choice with ! to persist it to bower.json

	? Answer 2

At least we have a major improvement over npm, as we are actully notified that something might break as we won't have all versions installed. Fortunately, there is a workaround - we can install those dependencies using a template `name=dep#version`:

	$ bower install jquery1=jquery#1.12.0 jquery#2.2.0 jquery3=jquery#3.0.0-alpha1
	bower cached        git://github.com/jquery/jquery-dist.git#2.2.0
	bower validate      2.2.0 against git://github.com/jquery/jquery-dist.git#2.2.0
	bower cached        git://github.com/jquery/jquery-dist.git#1.12.0
	bower validate      1.12.0 against git://github.com/jquery/jquery-dist.git#1.12.0
	bower cached        git://github.com/jquery/jquery-dist.git#3.0.0-alpha1
	bower validate      3.0.0-alpha1 against git://github.com/jquery/jquery-dist.git#3.0.0-alpha1
	bower install       jquery1#1.12.0
	bower install       jquery3#3.0.0-alpha1
	bower install       jquery#2.2.0

	jquery1#1.12.0 bower_components/jquery1

	jquery3#3.0.0-alpha1 bower_components/jquery3

	jquery#2.2.0 bower_components/jquery

And now we can find the package under `bower_components/jquery`, `bower_components/jquery1` and `bower_components/jquery3` folders.

### JSPM

Here we have a combination of npm's lack of conflict notification and Bower's easy workaround. If you want to install all versions at once, you get a pretty long output, but in the end you get only the last version installed:

	$ jspm install jquery@1.12.0 jquery@2.2.0 jquery@3.0.0-alpha1
	     Updating registry cache...
	     Looking up npm:jquery
	...
	ok   Installed jquery as npm:jquery@3.0.0-alpha1 (3.0.0-alpha1)
	     Clearing configuration for npm:jquery@2.2.0
	     Installed Forks
	                         npm:jquery 1.12.0 3.0.0-alpha1
	...
	ok   Install complete.

Now, if you read your command's output carefully, you could notice a subtle mentioning of conflict (called _forks_ here), but who has time to read it? It should be a prompt or even some error to have it fixed manually. Anyway, we can work it around, just like in Bower:

	$ jspm install jquery1=jquery@1.12.0 jquery@2.2.0 jquery3=jquery@3.0.0-alpha1
	     Updating registry cache...
	     Looking up npm:jquery
	ok   Installed jquery as npm:jquery@2.2.0 (2.2.0)
	...
	ok   Installed jquery3 as npm:jquery@3.0.0-alpha1 (3.0.0-alpha1)
	ok   Installed jquery1 as npm:jquery@1.12.0 (1.12.0)
	     Installed Forks
	                         npm:jquery 1.12.0 2.2.0 3.0.0-alpha1
     ...
	ok   Install complete.

You can see, that the output is almost identical.
