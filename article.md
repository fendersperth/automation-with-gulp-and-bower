# Automation with Gulp and Bower

Web development is definitely come a long way.

I was listening to a podcast this morning at work, http://stuffandnonsense.co.uk/blog/about/unfinished-business-episode-105-seventeen-coats-of-bullshit. It made me think about how my process has changed, the tools that I now use, and the struggle to find a good balance between configuration / setup and getting more productivity out of my work.

These days I like to use the command line. For a simple prototype, I would just setup something with Git for version control, Gulp for the build process and Bower to pull in my required libraries, I generally wouldn’t bother with a CMS or Back-end framework.

If you haven’t used tools like Gulp or Bower before, they probably could get in your way. It takes some learning to understand how to use these tools before you actually see any benefit. I wanted to write something brief to show a simple configuration for how I like to use these tools, and how they speed things up for me.

## Creating a new project

As mentioned above, I prefer working in the command line. I also prefer to only have a small set of applications open while I work. I use Sublime Text for editing my code and Chrome to view my work, most of the things I need are just in the terminal window.

I start off my project by creating a git repository and then initialising the node package manager (npm).

npm init

This sets up the `package.json` file. Without going into too much detail with this, it lets me store which modules I require for my project. This of course assumes node.js is installed on your system.

## The Tools

### Gulp

Gulp is a tool I use to automate my build process. It has been around for a while now, and there are many tutorials to explain how to get it installed. The `gulpfile.js` is where you config the build process.I generally re-use a `gulpfile.js` file from a previous project since my dev stack does not change often (html, scss, js) and if it does, I would likely modify an existing one.

To get started with my gulp configuration, I install my required modules like this,

npm i gulp-load-plugins ——save

This configuration is more for setting up a quick development environment. If you plan to use gulp to build something for production, you will likely need to customise thing in more detail.

### Bower

Bower is another tool I use often for my development environment. With Bower, I can specify the libraries that my project will require. I find that since the requirements for my project would vary, I recreate the bower configuration from scratch with each project.

Assuming that Bower has been installed, I initialise Bower for my install as below,

bower init

Like with npm, this will create a configuration file in my project root `bower.json`. To add a library to my project, I would type a command like,

bower install jquery ——save

This would add the latest version of jquery to my bower.json file and download jquery to a `bower_components` directory in my  project root. You can also specify a version number if you need 1.1* instead of 1.2*.

## Setting up the Gulp build

With the tools mentioned above, for it to all work together nicely, I would need gulp to do the following tasks:

- copy js library files from bower_components,
- compile my scss files,
- make sure my library and js files are loaded in the correct order,
- copy over all assets, 
- watch for changes in my scripts, html and scss files, and 
- reload the browser.

### Gulp modules

The modules I commonly use to make these happen in gulp are as below:

- gulp-load-plugins,
- gulp-del,
- gulp-sass,
- main-bower-files,
- gulp-order,
- gulp-inject,
- gulp-image, and
- browser-sync.

Other modules that could be useful for your project:

- gulp-cssmin,
- gulp-newer,
- gulp-autoprefixer,
- gulp-uncss, and
- gulp-uglify.

### File structure

Before going into the actual gulp configurations, I wanted to show my project’s file structure. There would typically be:

- three directories; `images`, `scripts` and `styles`,
- a `_build` directory where all the compiled, minified assets would outputted to, and
- a `vendors` which sits inside of `_build/js` for all the bower_components js library files. 

file structure

_build/
	css/
	img/
	js/
		vendor/
	index.html
images/
scripts/
styles/
index.html

index.html

<!doctype html>
<html>
<head>
<!— inject:css —>
<!— endinject —>
</head>
<body>
<!— inject:js —>
<!— endinject —>
</body>
</html>

### Setting up the tasks

Before I start going through how I do the configuration for each task, firstly note that I use the gulp-load-plugins module so that I can avoid calling each gulp module separately. Instead I call `plugins.sass()` for example, and the gulp-load-plugins module imports the sass module to the gulp build.

scss to css

gulp.task(‘styles’, function() {
    return gulp.src(‘./styles/app.scss’)
        .pipe(plugins.sass())
        .pipe(gulp.dest(‘./_build/css’));
});

minifying images

gulp.task(‘images’, function() {
    return gulp.src(‘./source/images/*.*’)
        .pipe(plugins.image())
        .pipe(gulp.dest(‘./_build/img’));
});

copying the scripts

gulp.task(‘scripts’, function() {
	return gulp.src(‘./scripts/**/*.js’)
                    .pipe(gulp.dest(‘./_build/app’))
});

copying the bower js libraries

gulp.task(‘bower’, function() {
    return gulp.src(mainBowerFiles({‘env’:’development’}))
                    .pipe(gulp.dest(‘./_build/js/vendor’));
});

inject the required files into index.html

gulp.task(‘html’, function() {
    var scriptLoadOrder =   [
                                ‘**/vendor/**/*.js’,
                                ‘**/script.js’,
                            ];
                            
    var sources = es.merge(
                  gulp.src([‘./_build/js/**/*.js’],{read:true}),
                  gulp.src([‘./_build/css/*.css’], {read: true})
                  )
                  .pipe(plugins.order(scriptLoadOrder));


    var stream = gulp.src([‘./index.html’])
                     .pipe(plugins.inject(sources,{‘ignorePath’:’/_build/‘}))
                    .pipe(gulp.dest(‘./_build’));
        
    return stream;
});

The `build` task simply waits for the individual tasks above to be completed.


gulp.task(‘build’, [’scripts’,’bower’,’styles’,’html’]);

The `watch` reloads the browser for changes in scripts or styles.

gulp.task(‘watch’, [‘build’], function() {
    gulp.watch(“./scripts/**/*.js”, [‘scripts’, browserSync.reload]);
    gulp.watch(“./styles/**/*.scss”, [‘styles’, browserSync.reload]);
    gulp.watch(“./index.html”, [‘html-inject’]);
});

web browser

gulp.task(‘browser-sync’, function() {

    var config = {
        open: false,
        port: 3000,
        notify: false,
        server: {
            baseDir: ‘_build’
        }
    };

    browserSync(config);
});

## The result

Have a video to show this all in action



