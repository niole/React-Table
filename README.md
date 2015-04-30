# RenderMarkdownWithReact-express-browserify-gulp-jade-tutorial
Render markdown with ReactJS using the express framework, browserify for dependency resolution, the gulp build system, and jade templating.

## Gulpfile
Setting up your views, routes directories, making your package.json: installing dependencies( node modules, express, gulp, browserify )...

1. set up directory tree
2. Set up `package.json` by running `npm init` at the top level of the app. Accept all the default options. This will result in an empty `package.json` file.
3. Next, we will tell `npm` to download all of the dependencies for this application and save them in a `node_modules` directory. We will install the necessary modules for using **Gulp**, a build system which will:
  - transform our JSX files in to JS and save output in `path.DEST_SRC` or `public/src/`.
  - copy our index.jade file from `path.JADE` or `frontend/index.jade` to `path.DEST` or `views/`.
  - it will watch for changes in all jade and JSX files (without gulp, we could have to do a lot more work every time we make changes to the app)
  We will also install the necessary modules for using **Browserify**, a CommonJS loader which oversees dependency resolution and allows you to require modules on the client side of your app. It bundles all .js files into a single file and updates the body of the index.jade file in `path.DEST` to contain just one rather than many of our self-made modules.

    ```sh
npm install --save-dev gulp;
npm install --save-dev gulp-concat;
npm install --save-dev gulp-uglify;
npm install --save-dev gulp-react;
npm install --save-dev gulp-html-replace;
npm install --save-dev gulp-inject;
npm install --save-dev gulp-streamify;
npm install --save-dev vinyl-source-stream;
npm install --save-dev vinyl-buffer;
npm install --save-dev browserify;
npm install --save-dev watchify;
npm install --save-dev reactify;
    ```
4. Now your package.json should be full of stuff. While you're here, you might as well also tell npm to download and install all the other things that we're going to need to make our app express/react/jade app run. Next run: 

    ```sh
npm install --save body-parser;
npm install --save cookie-parser;
npm install --save debug;
npm install --save dotenv;
npm install --save express;
npm install --save jade;
npm install --save morgan;
npm install --save react;
npm install --save serve-favicon;
    ```
5. At top level of the app, create **gulpfile.js** and save at top of file:

    ```javascript
var gulp = require('gulp');
var uglify = require('gulp-uglify');
var inject = require('gulp-inject');
var source = require('vinyl-source-stream');
var buffer = require('vinyl-buffer');
var browserify = require('browserify');
var watchify = require('watchify');
var reactify = require('reactify');
    ```
    As a tool, **Gulp** packages up code so that it can be transferred to a separate place from which **Browserify** can interact with it. To acheive this, our `gulpfile.js` must specify the paths/destination this code takes, the code being modified and how the code will be processed.
    
6. Next, in the gulpfile we create the **path** object:

    ```javascript
var path = {
  HTML: 'frontend/index.jade',
  MINIFIED_OUT: 'build.min.js',
  OUT: 'build.js',
  DEST: 'views/',
  DEST_BUILD: 'public/js/',
  DEST_SRC: 'public/src/',
  ENTRY_POINT: './frontend/App.jsx'
};
    ```

7. Next, we create the tasks that **Gulp** will complete and tell it what paths to use.

    ```javascript
gulp.task('default', ['watch']);

gulp.task('copy', function(){
  gulp.src(path.JADE)
  .pipe(gulp.dest(path.DEST));
});

gulp.task('watch', ['replaceHTMLsrc'], function() {
  gulp.watch(path.JADE, ['copy']);

  var watcher  = watchify(browserify({
    entries: [path.ENTRY_POINT],
    transform: [reactify],
    debug: true,
    cache: {}, packageCache: {}, fullPaths: true
  }));
  return watcher.on('update', function () {
    watcher.bundle()
    .pipe(source(path.OUT))
    .pipe(gulp.dest(path.DEST_SRC));
    console.log('Updated');
  })
  .bundle()
  .pipe(source(path.OUT))
  .pipe(gulp.dest(path.DEST_SRC));
});
    ```
    Gulp's default task is to watch and wait for changes in the JSX files which `path.ENTRY_POINT` specifies the top level of (This is just a sample of the code you need, copy the actual gulpfile.js code from the github repo). All of the other tasks `'replaceHTMLsrc'` and `'copy'` initiate the copying and modification of files by gulp. Browserify takes care of lumping these files together, following the dependency tree and modifying the JADE file at `path.DEST`. 
