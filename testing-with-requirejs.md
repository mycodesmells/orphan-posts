# Testing With RequireJS

Take a look on [a first post on RequireJS](http://mycodesmells.com/post/organize-frontend-scripts-with-requirejs/).

We've already seen how to organize your scripts with RequireJS, but what about testing it? It's not as trivial as testing your plain Java Script files, because of the way we declare things. Fortunately, Karma comes as a rescue making it all just a bit easier. Read on!

### Define vs plain object

Last time around, I showed how to create your own RequireJS modules using `define` keyword. Actually, it is necessary only if given script will be used later on as a dependency to the others and has dependencies of its own. In reality, though, it is recommended to prefix all your files this way, as it makes your codebase look consistent and well-organized. Let make a simple script then:

    // src/shouter.js
    define('shouter', function(){
      var self = {};

      self.shoutName = function(name) {
        return 'Howdy, ' + name;
      };

      return self;
    });

This will be our dependency, so we need a main script as well:

    // src/main.js
    define('main', ['shouter'], function(shouter) {
      var self = {};

      self.welcome = function(name, shoutout) {
        console.log('Welcome, ' + name);
        if(shoutout) {
          console.log(shouter.shoutName(name));
        }
      }
      return self;
    });

What now? Let's Karma!

### Karma init with RequireJS

Karma initialization process with RequireJS is very simple, as it has been designed to work well together. First install all necessary dependencies:

    npm install --save-dev karma karma-jasmine karma-requirejs karma-phantomjs-launcher

Optionally you can add `karma` or `test` script to your `package.json` file. Then initialize Karma and pay attention to a question regarding RequireJS, and bootstrap file:

    Which testing framework do you want to use ?
    Press tab to list possible options. Enter to move to the next question.
    > jasmine

    Do you want to use Require.js ?
    This will add Require.js plugin.
    Press tab to list possible options. Enter to move to the next question.
    > yes

    Do you want to capture any browsers automatically ?
    Press tab to list possible options. Enter empty string to move to the next question.
    > PhantomJS
    >

    What is the location of your source and test files ?
    You can use glob patterns, eg. "js/*.js" or "test/**/*Spec.js".
    Enter empty string to move to the next question.
    > src/**/*.js
    >

    Should any of the files included by the previous patterns be excluded ?
    You can use glob patterns, eg. "**/*.swp".
    Enter empty string to move to the next question.
    >

    Do you wanna generate a bootstrap file for RequireJS?
    This will generate test-main.js/coffee that configures RequireJS and starts the tests.
    > yes

    Do you want Karma to watch all the files and run the tests on change ?
    Press tab to list possible options.
    > yes


    RequireJS bootstrap file generated at "/tmp/requirejstest/test-main.js".

    Config file generated at "/tmp/requirejstest/karma.conf.js".

If you answered all the questions correctly, you should find `test-main.js` script in your root directory. Its contents might be a bit mysterious, but we just need to edit one place - add `paths` to the configuration, so that our tests are visible to RequireJS:

    // test-main.js
    require.config({
      ..
      paths: {
        'shouter': 'src/shouter',
        'main': 'src/main'
      },
      ...
    })

### Writing tests

Here comes an easy part! We're down to coding tests, finally! The only trick here is to call `require` whenever you need to load some script to test. It's good practice to do it in `beforeEach` section and assign them to variables that are later to be used in each test. *Don't forget about adding `done` there, as loading scripts is asynchronous!*

    // src/shouter.spec.js
    describe('shouter', function() {
    	var shouter;

    	beforeEach(function(done){
    		require(['shouter'], function(_shouter) {
    			shouter = _shouter;

    			done();
    		});
    	});

    	it('should shout my name', function() {
    		expect(shouter.shoutName('Pawel')).toEqual('Howdy, Pawel');
    	});
    });

Given the fact that all scripts are loaded once, you can easily spy on dependencies to check if they are called correctly:

    // src/main.spec.js
    describe('main', function() {
    	var main, shouter;

    	beforeEach(function(done){
    		require(['main', 'shouter'], function(_main, _shouter) {
    			main = _main;
    			shouter = _shouter;

    			spyOn(shouter, 'shoutName').and.callThrough();

    			done();
    		});
    	});

    	it('should call shoutout', function() {
    		main.welcome('Pawel', true);

    		expect(shouter.shoutName).toHaveBeenCalledWith('Pawel');
    	});

    	it('should not call shoutout', function() {
    		main.welcome('Pawel', false);

    		expect(shouter.shoutName).not.toHaveBeenCalled();
    	});
    });

Unfortunately, spying without `.and.callThrough` might break your tests somewhere else (where you can actually depend on the function to behave in a specific way). You can, however, undo your spying changes with `require.undef(..)`:

    // src/main.spec.js
    describe('main', function() {
        beforeEach(function(done){
            require(['main', 'shouter'], function(_main, _shouter) {
                ...
            });
        });

        afterEach(function(){
            require.undef('main');
            require.undef('shouter');
        });

        ...
    });

This way the scripts are reloaded on every test and any spying or behaviour change is disabled once given test is over.
