# Automate all the things

Web development is definitely come a long way.

I was listening to a podcast this morning at work, http://stuffandnonsense.co.uk/blog/about/unfinished-business-episode-105-seventeen-coats-of-bullshit. It made me think about how my process has changed, the tools that I now use, and the struggle to find a good balance configuration / setup and getting more productivity out of my work.

These days I like to use the command line. For a simple web project, a static website, I would just setup something with Gulp and Bower. I would use git to version control my code, and if avoidable, I wouldn’t bother with a CMS or framework.

If you haven’t used tools like Gulp or Bower before, they probably could get in your way. It takes some investment to understand how to use these tools before you actually see any benefit. I wanted to write something brief to show a simple configuration for how I like to use these tools.

## Creating a new project

I prefer working in the command line and having a small set of applications open. I use Sublime Text 2 for editing my code and Chrome to view my work, everything else is in the terminal window.

I start off my project with,

npm init

This sets up the package.json file. Without going into too much detail with this, it lets me store which tools (modules) I require for my project. This of course assumes node.js is installed on your system.

## The Tools

### Gulp

I then move on to setting up Gulp. I generally re-use a gulpfile.js file. My dev stack does not change often, if it goes, I would likely modify / re-write the gulpfile.js as required.

I install my required modules like this,

npm i gulp-plugins —save

This blog is really a guide for setting up a dev environment. If you plan to use gulp to build something for production, you will likely need to customise thing in more detail.

#### Gulp modules

The plugins I use for gulp are as below;

- gulp-plugins,
- gulp-del,
- gulp-sass,
- main-bower-files,
- gulp-order,
- gulp-inject,
- gulp-imagemin and
- browser-sync.

Other modules that I don’t currently use, but could be useful for your project;

- gulp-uncss and
- gulp-uglify.

#### Gulp tasks

My basic dev build task will do the following tasks:

- clean build the destination,
- compile the scss and copy to build destination,
- copy over the bower files to build destination,
- order and inject js/css files into html, copy html files to build destination, and
- copy images to build destination.

I then have a watch task which will do an initial build, then watch for changes:

- call build task,
- watch for asset changes and
- inject new assets into the browser.

### Bower

Now that my gulp stuff is setup, I can get to work. I can add JS libraries to the project via bower when I need them, they will automatically be injected into the index.
Starting bower.

bower init

Adding a JS library.

bower i jquery —save

I won’t go into the installation or configurations of bower in this post, generally I use the default settings.

### File setup

#### File structure

_build/
css/
img/
js/
vendor/
index.html
images
scripts
styles
index.html

#### index.html

<!doctype html>
<html>
<head>
…
<!— inject:css —>
<!— endinject —>
</head>
<body>
…
<!— inject:js —>
<!— endinject —>
</body>
</html>





