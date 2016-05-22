# Taking Advantage of Sass using gulp

Last week we've taken a look on Sass - an awesome CSS preprocessor that really makes working with styles easier. As cool as it is, it would be tough to use it as a CLI tool only. Fortunately we can take full advantage of this tool as a part or an usual JavaScript development workflow, that is using Gulp. Let's dive in!

Of course, we could use Sass command as a npm script and put it into our `package.json` file:

    ...
    "scripts": {
        "sass": "sass input.sass output.css"
    }
    ...

The problem with this, tough, is that it cannot be included into gulp tasks, therefore it would be hard to make a single build command that would prepare the application from various sources (styles, scripts, etc).

### npm Dependencies to the rescue

Fortunately, there is already a way to solve this prolem usin gulp and npm dependencies. Obviously, using 3rd party solutions can be risky (even though there is a way to avoid those problems), but it might be even more difficult to reinvent the wheel and do it yourself.

First step is to install `gulp-sass` dependency:

    npm install gulp gulp-sass --save-dev

Then, include it into your gulp file and you can already see Sass in action:

    var gulp = require('gulp');
    var sass = require('gulp-sass');

    gulp.task('sass', function () {
      return gulp.src('./sass/**/*.sass')
        .pipe(sass().on('error', sass.logError))
        .pipe(gulp.dest('./css'));
    })

With our simple `file.sass` file:

    // sass/file.sass
    $mainColor: red

    body
      color: $mainColor

We can now run our `sass` task:

    $ gulp sass
    [21:48:18] Using gulpfile /path/to/project/sass/gulpfile.js
    [21:48:18] Starting 'sass'...
    [21:48:18] Finished 'sass' after 22 ms

We have an output `file.css` created:

    // css/file.css
    body {
      color: red; }

### Adding browser prefixes automatically

This already sounds pretty cool, but there is one thing that raw Sass does not do: add browse-specific prefixes to some CSS attributes. It would be tough to keep all browser-specific quirks in mind, so you should use some tool to do it automatically for you. And if there is a need in JavaScript world, there has to be an npm dependency to satisfy it already, right? Here is where `gulp-autoprefixer` comes to play. It is designed to work on CSS files, so it has to be injected as a pipe right after `gulp-sass` transforms our sources into CSS.

    // gulpfile.js
    ...
    var autoprefixer = require('gulp-autoprefixer');

    gulp.task('sass', function () {
      return gulp.src('./sass/**/*.sass')
        .pipe(sass().on('error', sass.logError))
        .pipe(autoprefixer())
        .pipe(gulp.dest('./css'));
    });
    ...


A classic example where _autoprefixer_ can really make your life easier is setting `display` property to `flex`, which is still not fully supported across browsers:

    // sass/file.sass
    .container
      display: flex

After running our task you can see an enhanced output:

    // css/file.css
    .container {
      display: -webkit-box;
      display: -ms-flexbox;
      display: flex; }

### Summary

As you can see, adding Sass to your gulp workflow is very easy. With a couple of configuration lines you get a powerful tool working fast and really make your worry less about creating styles for your application. I recommend you to start using Sass with gulp as soon as you can.
