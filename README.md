# Workshop - Gulp
![gulp](https://avatars0.githubusercontent.com/u/6200624?v=3&s=400 "gulp")

As a result of this wokshop you will know how to use Gulp task runner to automate your front-end development workflow.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Introduction](#introduction)
- [What you need](#what-you-need)
- [How it works](#how-it-works)
  - [package.json](#packagejson)
  - [Installing Gulp and Gulp plugins](#installing-gulp-and-gulp-plugins)
  - [gulpfile](#gulpfile)
      - [Loading gulp plugins](#loading-gulp-plugins)
      - [Setting up tasks](#setting-up-tasks)
    - [Files](#files)
    - [Globbing patterns](#globbing-patterns)
  - [Running gulp tasks](#running-gulp-tasks)
- [Let's get the party started!](#lets-get-the-party-started)
  - [Installation & Basic setup](#installation-&-basic-setup)
    - [1. Installing Node.js and updating npm](#1-installing-nodejs-and-updating-npm)
    - [2. Installing gulp command line interface (CLI)](#2-installing-gulp-command-line-interface-cli)
    - [3. Creating package.json](#3-creating-packagejson)
    - [4. Creating gulpfile](#4-creating-gulpfile)
    - [5. Installing gulp and gulp plugins](#5-installing-gulp-and-gulp-plugins)
  - [Tasks](#tasks)
    - [1. Compile SAS files to CSS](#1-compile-sass-files-to-css)
    - [2. Create sprite image](#2-create-sprite-image)
    - [3. Minify CSS](#3-minify-css)
    - [4. Lint JavaScript using JSHint](#4-lint-javascript-using-jshint)
    - [5. Concat JavaScript](#5-concat-javascript)
    - [6. Minify JavaScript](#6-minify-javascript)
    - [7. Minify images](#7-minify-images)
    - [8. Copy fonts](#8-copy-fonts)
    - [9. Run predefined tasks whenever files are changed](#9-run-predefined-tasks-whenever-files-are-changed)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction
* Gulp - the JavaScript **task/build runner**
* Great for **automation** and for improving your front-end development workflow
* You have to do less work when performing repetitive tasks like minification, compilation, unit testing, linting, etc
* Gulp and Gulp plugins - installed and managed via **npm** (the Node.js package manager)

## What you need
* Node.js: [https://nodejs.org/](https://nodejs.org/)
* npm: [https://www.npmjs.com/](https://www.npmjs.com/)
* Gulp: [https://www.npmjs.com/package/gulp](https://www.npmjs.com/package/gulp)
* Gulp Plugins: [http://gulpjs.com/plugins](http://gulpjs.com/plugins)
* enthusiasm :stuck_out_tongue_winking_eye:

## How it works

A typical setup will involve adding two files to your project: `package.json` and the `gulpfile.js`

### package.json
This file is used by [npm](https://npmjs.org/) to store metadata for projects published as npm modules. It is also responsible for managing dependencies. The [devDependencies](https://docs.npmjs.com/files/package.json#devdependencies) property defines the different packages that are needed for your application.

Sample `package.json`:
```javascript
{
  "name": "my-project-name",
  "version": "0.1.0",
  "devDependencies": {
    "gulp": "^3.9.0",
    "gulp-jshint": "^1.11.2",
    "gulp-uglify": "^1.4.1"
  }
}
```

### Installing gulp and gulp plugins
Gulp and Gulp plugins are installed with the command `npm install <module> --save-dev`. Not only will this install `<module>` locally, but it will automatically be added to the `devDependencies` section in `package.json` file, using a [tilde version range](https://docs.npmjs.com/misc/semver#advanced-range-syntax).

For example, this will install the latest version of gulp in your project folder, adding it to your `devDependencies`:

`npm install gulp --save-dev`

The same can be done for gulp Plugins and other node modules. As seen in the following example installing the JSHint task module:

`npm install gulp-jshint --save-dev`

### gulpfile
The `gulpfile.js` is a valid JavaScript file that belongs in the root directory of your project, next to the `package.json` file. It is used to configure or define tasks and load gulp plugins.

gulpfile is comprised of the following parts:
* Loading gulp plugins
* Setting up tasks

Sample `gulpfile.js`:
```javascript
var gulp = require('gulp'),
    uglify = require('gulp-uglify');

gulp.task('compress', function() {
  return gulp.src('src/js/*.js')
    .pipe(uglify())
    .pipe(gulp.dest('dist'));
});
```

##### Loading gulp plugins
As long as a plugin has been installed via `npm install` and is specified in `package.json` as a dependency, it may be simple enabled inside your `gulpfile.js`:
```javascript
// Load the plugins that provides the "uglify" and "concat" task.
var gulp = require('gulp'),
    uglify = require('gulp-uglify'),
    concat = require('gulp-concat');
```

##### Setting up tasks and configuration
This is handled by the `gulp.task` method. This method should be passed an function containing plugin initialization with configurations as parameters.
```javascript
var gulp = require('gulp'),
    uglify = require('gulp-uglify'),
    concat = require('gulp-concat');

gulp.task('compress', function() {
  var options = {
    foo: '...'
  };
  return gulp.src('src/js/*.js')
    .pipe(uglify(options))
    .pipe(concat({...}))
    .pipe(gulp.dest('dist'));
});
```

#### Files
There are several ways to define **src-dest** (source-destination) file mappings, offering varying degrees of verbosity and control. Any multi task will understand all the following formats, so choose whichever format best meets your needs.

```javascript
// Path to source files
gulp.task('css', function() {
    return gulp.src('src/css/*.css')
        .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'))
        .pipe(gulp.dest('dist/css'));
});

// Paths in array
gulp.task('css-min', function() {
    return gulp.src(['src/css/*.css', 'src/vendor/**/*.css'])
        .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'))
        .pipe(gulp.dest('dist/css'));
});

```

For more information about configuration check in current npm plugin guide. It has an in-depth explanation on how to configure task inside the `gulpfile.js`.

#### Globbing patterns
gulp supports filename expansion (also know as globbing). In a filepath:
* `*` matches any number of characters, but not `/`
* `?` matches a single character, but not `/`
* `**` matches any number of characters, including `/`, as long as it's the only thing in a path part
* `{}` allows for a comma-separated list of "or" expressions
* `!` at the beginning of a pattern will negate the match

All most people need to know is that `foo/*.js` will match all files ending with `.js` in the `foo/` subdirectory, but `foo/**/*.js` will match all files ending with `.js` in the `foo/` directory and _all of its subdirectories_.

### Running gulp tasks
Gulp task can be run with command `gulp taskName`.

## Let's get the party started!

### Installation & Basic setup
Download ZIP package from this page and unpack it.

#### 1. Installing Node.js and updating npm
* Visit [https://nodejs.org/download/](https://nodejs.org/download/) and grab the installer for your operating system
* Test: `node -v`
* Node comes with npm installed, however, npm gets updated more frequently than Node does, so make sure you have the latest version
* `npm install -g npm` - you may need to use sudo or run your command shell as Administrator (for Windows) to do this
* Test: `npm -v`

#### 2. Installing gulp
* Responsible for locating the gulp library in your project and loading the **gulpfile.js** configuration
* It will put the `gulp` command in your system path, allowing it to be run from any directory.
* `npm install -g gulp` - you may need to use sudo or run your command shell as Administrator (for Windows) to do this
* The `-g` flag will install `gulp` globally so you will only ever have to run this command once

#### 3. Creating package.json
In the root directory of your project create file `package.json` with this content:
```javascript
{
  "name": "gulp-workshop",
  "version": "0.0.1"
}
```

*Note: the command `npm init` could instead be used to automatically generate a `package.json` file, but doing so adds many properties that we won’t need to use in this tutorial, so creating it manually is a simpler option in this case.*

#### 4. Creating gulpfile
Create `gulpfile.js` in the root directory of your project with this content:
```javascript
// Load plugins here
// ## Your code here ##

// Setting up your tasks
// ## Your code here ##

```

#### 5. Installing gulp and gulp plugins
For the purpose of this tutorial we will install gulp and these plugins:
* gulp-concat
* gulp-copy
* gulp-cssmin
* gulp-imagemin
* gulp-jshint
* gulp-ruby-sass
* gulp-uglify
* gulp-watch
* gulp.spritesmith
* gulp-rename

So go to the root directory of your project and install the latest version of gulp and all plugins with this single command:

`npm install gulp gulp-concat gulp-copy gulp-cssmin gulp-imagemin gulp-jshint gulp-ruby-sass gulp-uglify gulp-watch gulp.spritesmith gulp-rename imagemin-pngquant --save-dev`

Now check your project directory and you should see a new folder `node-modules` with all of our development dependencies that we just installed. A `devDependencies` property is also added to `package.json`, listing the names and current version numbers of our application’s development dependencies:
```javascript
{
  "name": "gulp-workshop",
  "version": "0.0.1",
  "devDependencies": {
    "gulp": "^3.9.0",
    "gulp-concat": "^2.6.0",
    "gulp-copy": "0.0.2",
    "gulp-cssmin": "^0.1.7",
    "gulp-imagemin": "^2.3.0",
    "gulp-jshint": "^1.11.2",
    "gulp-ruby-sass": "^2.0.4",
    "gulp-uglify": "^1.4.1",
    "gulp-watch": "^4.3.5"
  }
}
```

### Tasks
1. Open `index.html` in your browser

  <img src="http://cdn.grumpycats.com/wp-content/uploads/2015/07/11701195_885927281443010_75980707707035797_n-300x300.jpg" width="250" />

  But that's ok, see explanation in the next step :smiley:
2. Check the HTML markup of `index.html`. We are referencing directories and files that don’t exist yet – `dist/css/sprites.css`, `dist/css/main.css`  and `dist/js/main.js`. These directories and files will be created automatically by gulp once we set it up and run tasks.

#### 1. Compile SASS files to CSS
**Plugin:** [gulp-ruby-sass](https://www.npmjs.com/package/gulp-ruby-sass)

As the first task we are going to make our page little bit less ugly so Grumpy Cat is happy (not impossible! :smirk:). Check `src/sass` directory. There are SASS files we need to compile to CSS.

1. Create a task `sass` with some target name that will take files `src/sass/base.scss` and `src/sass/header/header.scss` and will compile them to file `main.css` in the `dist/css` directory. We won't use any special options.
2. Enable plugin `gulp-ruby-sass` by adding `var sass = require('gulp-ruby-sass');` inside your `gulpfile.js`
3. Your `gulpfile.js` should look like this now (when compact form used for files):

  ```javascript
  // Load plugins here
  var gulp = require('gulp');
      concat = require('gulp-concat'),
      sass = require('gulp-ruby-sass');
  //Setting up tasks
    gulp.task('sass', function () {
      return sass('src/sass/*.scss')
        .on('error', sass.logError)
        .pipe(concat('main.css'))
        .pipe(gulp.dest('dist/css'));
    });
  ```
Note: to install sass, you need execute command 'sudo gem install sass'
4. Run the following command:

  `gulp sass`

  New directory `dist/css/` was created and you should see file `main.css` which has content of both SASS files we specified in the task.
5. We have our CSS ready (apart from sprite CSS that we will create in the next task) so check your browser and Grumpy Cat should be happy.
6. Now let's assume we want `base.sass` to be always the first one in the created CSS file but then we want all SASS files from `src/sass/header/` folder. With current configuration we would need to manually update `src` property in our task everytime some SASS file was added or deleted.

  Since we don't want to do that manually modify this part `'src/sass/header/header.scss'` in `src` property so it will compile **all** SASS files inside `src/sass/header/` directory and its subdirectories.
7. Create some new SASS file in the `src/sass/header/` directory. If you run command `gulp sass` now, your `main.css` file should contain all three compiled SASS files.

#### 2. Create sprite image
**Plugin:** [gulp.spritesmith](https://www.npmjs.com/package/gulp.spritesmith)

Check source code of `index.html`. In the header there are tags for 3 social icons, that do not have icons at the moment. In `src/sprites/` directory we already have 2 icons ready, `twitter.png` and `instagram.png`, that we need to add to a sprite image and create some CSS rules. (The third icon will be added later in another task).

1. Create a task `sprite` with some target name that will create the sprite image with name `spritesheet.png` using all PNG images in the directory `src/sprites/` and will save this sprite image in the directory `dist/images/`. CSS file should have name `sprites.css` and should be created in `dist/css/` directory.

2. Enable plugin `gulp.spritesmith` inside your `gulpfile.js`
3. Now create a sprite image and CSS using this command: `gulp sprite`
4. Check `dist/images/` directory. You should see a new file `spritesheet.png`.
5. Check `dist/css/` directory. You should see a new file `sprites.css`. Icon classes are named after their original file names, so you will have classes `.icon-instagram` and `.icon-twitter`
6. Refresh your browser, icons are displayed.

#### 3. Minify CSS
**Plugin:** [gulp-cssmin](https://www.npmjs.com/package/gulp-cssmin)
**Plugin:** [gulp-rename](https://www.npmjs.com/package/gulp-rename)

In `dist/css/` directory we have CSS files we want to minify.

1. Create a task `cssmin` with some target name that will minify all CSS files in `dist/css/` and create files with extension `.min.css` in the same directory. Exclude files that already have `.min.css` extension. You will need to build the files object dynamically like:

  ```javascript
gulp.task('cssmin', function () {
    gulp.src(['', '']) // Actual pattern to match - first value should match all .css files, second value should exclude all .min.css files
        .pipe(cssmin())
        .pipe(rename())
        .pipe(gulp.dest(''));
});
  ```

2. Enable plugin `gulp-cssmin` and `gulp-rename` inside your `gulpfile.js`
3. Now minify our CSS file with the following command `gulp cssmin`
4. Check `dist/css/` directory. You should see new files `main.min.css` and `sprites.min.css`. From now on we can use these minified versions on our site, so open `index.html` and change src of our CSS files to minified versions.
5. Check your browser if everything is ok (if Grumpy Cat is still happy).
6. Register a new task with name `build-css` in your gulpfile that will run our 3 tasks related to CSS in this order: `sass`, `sprite` and `cssmin`:

  ```javascript
  gulp.task('name-of-custom-task', ['task1', 'task2', 'task3']);
  ```
7. Now you can use only one command `gulp build-css` that will run all 3 defined tasks.

#### 4. Lint JavaScript using JSHint
**Plugin:** [gulp-jshint](https://www.npmjs.com/package/gulp-jshint)

In `src/js/` directory we have few JavaScript files we want to validate with [JSHint](http://www.jshint.com/).

1. Create a task `jshint` with some target name that will validate all JavaScript files in `src/js/`. You can specify any option that [JSHint](http://www.jshint.com/) supports. See the [JSHint documentation](http://jshint.com/docs/options/) for a list of supported options.

  Options can be set directly in `options` parameter of `jshint` task or you can specify [jshintrc](https://www.npmjs.com/package/gulp-jshint#jshintrc) in `options` that can be set to `true` or to a specific file with those options for JSHint. For this tutorial we will use just few options directly in `jshint` task:

  ```javascript
    var jshint = require('gulp-jshint');
    var gulp   = require('gulp');

    gulp.task('lint', function() {
      return gulp.src('./lib/*.js')
        .pipe(jshint())
        .pipe(jshint.reporter('YOUR_REPORTER_HERE'));
    });
  ```

2. Enable plugin `gulp-jshint` inside your `gulpfile.js`
3. Lint JavaScript files with the following command: `gulp jshint`
4. Oops! As you can see JSHint detected some errors and potential problems in our code. So read what's wrong and fix these issues.
5. Now run `gulp jshint` again. If you fixed all mentioned issues, it should run without any warning.

#### 5. Concat JavaScript
**Plugin:** [gulp-concat](https://www.npmjs.com/package/gulp-concat)

Now when our JavaScript code doesn't have any errors, we can concatenate these files.

1. Create a task `concat` with some target name that will concatenate all JavaScript files in `src/js/` and will create a new file `main.js` in directory `dist/js/`:
  ```javascript
    var concat = require('gulp-concat');

    gulp.task('concat', function() {
      return gulp.src('lib/*.js')
        .pipe(concat())
        .pipe(gulp.dest('dist'));
    });
  ```

2. Enable plugin `gulp-concat` inside your `gulpfile.js`
3. Run task: `gulp concat`
4. Check directory `dist/js/`. There should be a new file with name `main.js`.
5. Check your browser. JavaScript should be working and you should see a new message on the page.

#### 6. Minify JavaScript
**Plugin:** [gulp-uglify](https://www.npmjs.com/package/gulp-uglify)

In `dist/js/` we have concatenated JavaScript files in `main.js`. Now we want to minify this file with UglifyJS.

1. Create a task `uglify` with some target name that will minify `main.js` in `dist/js/` and will create a new file `main.min.js` in the same directory.
  ```javascript
    var uglify = require('gulp-uglify');

    gulp.task('uglify', function() {
      return gulp.src('lib/*.js')
        .pipe(uglify())
        .pipe(gulp.dest('dist'));
    });
  ```

2. Enable plugin `gulp-uglify` inside your `gulpfile.js`
3. Run task: `gulp uglify`
4. Check directory `dist/js/`. There should be a new minified file with name `main.min.js`. From now on we can use this minified version on our site, so open `index.html` and change src of our JavaScript file from `main.js` to `main.min.js`.
5. Check your browser if everything is ok.
6. Register a new task with name `build-js` in your gulpfile that will run our 3 tasks related to JavaScript in this order: `jshint`, `concat` and `uglify`:

  ```javascript
  gulp.task('name-of-custom-task', ['task1', 'task2', 'task3']);
  ```
7. Now you can use only one command `gulp build-js` that will run all 3 defined tasks.

#### 7. Minify images
**Plugin:** [gulp-imagemin](https://www.npmjs.com/package/gulp-imagemin)

In `src/images` we have few images that we want to minify.

1. Create a task `imagemin` with some target that will optimise all images in `src/images` and will save them to `dist/images`. We will build the files object dynamically:

  ```javascript
    var gulp = require('gulp');
    var imagemin = require('gulp-imagemin');
    var pngquant = require('imagemin-pngquant');

    gulp.task('default', function () {
        return gulp.src('src/images/*')
            .pipe(imagemin({
                progressive: true,
                svgoPlugins: [{removeViewBox: false}],
                use: [pngquant()]
            }))
            .pipe(gulp.dest('dist/images'));
    });
  ```
2. Enable plugin `gulp-imagemin` inside your `gulpfile.js`
3. Run task: `gulp imagemin`.
4. Check directory `dist/images/`. There should be new optimised images.

#### 8. Copy fonts
**Plugin:** [gulp-copy](https://www.npmjs.com/package/gulp-copy)

In `src/fonts` we have fonts that we want to copy to the `dist/fonts` directory.

1. Create a task `copy` with target `fonts` that will copy all files from `src/fonts` to the `dist/fonts` directory. No hint provided for this task.
2. Enable plugin `gulp-copy` inside your `gulpfile.js`
3. Run task: `gulp copy`.
4. Check directory `dist/fonts/`. There should be fonts files copied from the source directory.

#### 9. Run predefined tasks whenever files are changed
**Plugin:** [gulp-watch](https://www.npmjs.com/package/gulp-watch)

In all previous tasks we had to run `gulp` command manually everytime when we wanted some task to be run. But we can use other great plugin that will run predefined tasks whenever watched file patterns are added, changed or deleted.

1. Create a task `watch` with targets `sass`, `css`, `sprite`, `js` and `imagemin` and for each target set up `files` that should be watched and correct `tasks` that should be run.
  ```javascript
  var gulp = require('gulp'),
    watch = require('gulp-watch');

    gulp.task('stream', function () {
        return gulp.src('css/**/*.css')
            .pipe(watch('css/**/*.css'))
            .pipe(gulp.dest('build'));
    });

    gulp.task('callback', function (cb) {
        watch('css/**/*.css', function () {
            gulp.src('css/**/*.css')
                .pipe(watch('css/**/*.css'))
                .on('end', cb);
        });
    });
  }
  ```

2. Enable plugin `gulp-watch` inside your `gulpfile.js`
3. Register `watch` task to be a `default` task.
4. Run the default task with this command: `gulp`. You should see `Waiting...`. So we know watch task is waiting for some changes to be made.
5. So let's test it. Check `index.html`. In the header there is already the tag for Facebook icon but we need to add it to the sprite image and CSS. So go to the following URL [https://dl.dropboxusercontent.com/u/15053734/grunt-workshop/facebook.png](https://dl.dropboxusercontent.com/u/15053734/grunt-workshop/facebook.png). Download and save this Facebook icon in the `src/sprites` directory. Related tasks should be run automatically. Check your browser, Facebook icon should be displayed.

  <img src="https://dl.dropboxusercontent.com/u/15053734/grunt-workshop/lemon.png" width="200" />
6. You can also try to change SASS, JavaScript or other files and all defined tasks should be run automatically as you are making your changes.


<img src="https://dl.dropboxusercontent.com/u/15053734/grunt-workshop/thats-all-folks.jpg" width="400" />










