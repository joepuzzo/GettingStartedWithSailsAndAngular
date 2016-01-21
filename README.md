# Getting Started With Angular & Sails

## Introduction

So, you want to build an angular application, but need a backend to serve it up. You have looked into many solutions including Java Spring, Ruby on Rails, Nodes express, etc and now you have to make a decision. While you could go many directions here, you have some requirements.  

You want:

1.  a full stack JS application.
2. a simple lightweight solution.
3. minimal configuration.
4. to play well with angular.
5. a simple test framework.   
6. a simple way to manage dependencies.
7. a simple way to build your application. 
8. to know what the heck is going on! 

So lets look at some of our contenders: 

* Spring framework: its website claims to be "simple, portable, fast, and flexible", but it runs on Java and isn't as lightweight as some of its competitors. It also won't lead to a full stack JS application. 
* Ruby on rails: "easy and fun" and follows the "convention over configuration" methodology, but still isn't a full JS stack.  
*  Express is a widely-adopted backend web framework written in on top of Node. This seems like just the thing, but it requires some initial configuration and does not come with a build system out of the box. 

If we're interested in the NodeJS/Express approach, how can we simplify configuration and introduce a build system? One widely-used solution is the yeoman generator, which is "a generic scaffolding system allowing the creation any kind of app". Its a node module that you can install using npm and can use as a tool to setup an application. There are thousands of generators for various project types. All we have to do is select a generator and run `yo generator-name` to generate a base project. 

Unfortunately, yeoman lacks standardized configuration. You could have a yeoman generator called  `angular-meanstack` and a generator called `meanstack-angular` that both create very similar applications but they are structured very differently, since all decisions were up to the author of the generator. Additionally, this approach has some drawbacks for newcomers; the wealth of generated files makes it difficult to sift through and understand how the application works. If you are familiar with all the tools then this is not bad, but for others it can pose a challenge. In the end you are left with a "template" application that has a bunch of stuff setup for you, but unless you have used that generator before, you really don't know what you have. 

So are left with a dilemma. Yeoman seems like a holy grail because it satisfies nearly all of our requirements. 

1. a full stack JS application. <font color='green'>✓</font> 
2. a simple lightweight solution. <font color='green'>✓</font> 
3. minimal configuration. <font color='green'>✓</font>
4. to play well with angular. <font color='green'>✓</font>
5. a simple test framework. <font color='green'>✓</font> 
6. a simple way to manage dependencies.  <font color='green'>✓</font>
7. a simple way to build your application. <font color='green'>✓</font> 
8. to know what the heck is going on! <font color='red'>✗</font>

Given that we're interested in truly understanding how our application works, let's consider an alternative to yeoman-- a more standardized framework called Sails.

## Sails

Sails is a "convention over configuration" MVC (ModelViewController) backend framework that is written on top of express. It can be used to create simple APIs or full stack applications. Similar to Ruby on Rails, Sails has a general structure that is used for all types of applications, and has abstracted out much of the gross setup to present a more elegant structure. This means that we as developers will spend less time configuring and more time developing. 

While sails can be used to create custom APIs and microservices,  we are going to focus on using it as a backend for our angular application. Out of the box sails is setup as a server-rendered application (old fashioned approach). This means that all routing is done on the server side and the server (Sails) is responsible for "serving up" our views. So when a browser sends a GET request to http://yoururl/home this request is mapped to the sails routes that will map your request to a view and send it back to your browser.  If there are no routes defined, sails will then look into an assets folder for an index.html page and return that instead. Because we are using angular, where all routing is done internally within the browser (new approach), there is no need for the server rendered approach. Therefore, we will simply be using sails as a "static asset application". Thankfully, this is a built-in option.  

One thing to note before we start setting up our first application, is that Sails is a great solution for all your backend micro services. It was designed to be the node backend, and has abstracted everything out in a beautiful way that allows you to easily get your services up and running right out of the box. So while we are going to use it to render our angular app, you should definitely also consider it for writing your services! 

## Setting Things Up  

This process will require a little more configuration than a yeoman generator, but will leave you with a much better understanding of how your application operates. This tutorial assumes you have worked with node before and are familiar with the npm package manager. 

In the following steps, we will

1. install Sails
2. use sails to generate our new application
3. setup sails as a static asset application 
4. install Bower
5. use Bower to install angular and some additional fronted dependencies
6. setup a basic angular application within sails
7. show how sails can automatically add dependences to angular app
8. install a test framework and get our first test app and running 

The instructions go into extra detail in some places, but this process would normally only take around 15 minutes and a few commands in the terminal. 

### Installing Sails

First we need to get sails installed. To do this, simply run the following command: 

```bash 
$ npm install -g sails
```

You should now have sails installed on your machine. If you are having problems, try prefixing the command with `sudo`. If you're behind a proxy, make sure you have set up the proxies in the ~/.npmrc file.


### Generate our first application

Create a new test project by running the following command: 

```bash 
$ sails new testProject
```

You now have your first fully functional sails application. Now go into your new project and start up the app with the following command: 

```bash
$ cd testProject
$ npm install
$ sails lift
```

Open a browser and hit http://localhost:1337. You should see the default sails page in the browser. 


### Prepare for angular application

As we previously discussed, Sails is by default a server-rendered application. We are going to turn it into a static asset application. Before we do, lets look at the current structure of our project. When you `ls`in the testProject directory, you will see the following structure
```bash
Gruntfile.js    api     assets      node_modules    tasks
README.md   app.js      config      package.json    views
```
We won't discuss the purpose of each of these files and directories in detail, but a basic understanding will be helpful. The descriptions below were taken strait from Sails website. For full descriptions, go to http://sailsjs.org/documentation/anatomy/my-app. 

* api
    * This folder contains the vast majority of your app's back-end logic. It is home to the 'M' and 'C' in MVC Framework. 
*  assets 
    * This is your assets folder. It houses all of the static files that your app will need to host. Feel free to create your own files and folders in here. Upon lifting, a file called `myApp/assets/newFolder/data.txt` could be accessed at http://localhost:1337/newFolder/data.txt.
* config
    * This folder contains various files that will allow you to customize and configure your Sails app.
* tasks
    * Sails uses Grunt for asset management. This file contains configuration information for the GRUNT tasks that Sails for this purpose.
* views 
    * This is the directory that holds all of your custom views. To create a custom view, create a new directory inside of this then create a new .ejs or .html file. In order for it to be rendered by a client, you must either set up a route in myApp/config/routes.js or use the res.view() method inside of a custom controller action.
* package.json
    * This is a standard configuration file for npm. Among other things, this file contains the name and version of all of the Node Modules that your app depends on to run.
* README
    * This is a generic README that you can edit to describe your app.
* .gitignore
    * A default .gitignore file that has all commonly ignored files and directories
* .sailsrc
    * This file is useful for setting configuration for ALL Sails apps on a computer. You can also use it to extend the functionality of the Sails CLI tool.
* app.js
    * This file exists only to tell Node how to start your app. It is used once and only when you lift your app from the command line. You should just ignore this file.
* Gruntfile.js
    * Sails uses Grunt for asset management. This file contains configuration information for the GRUNT tasks that Sails uses for this purpose.
Sails' integration with Grunt is fully customizable but for most use cases, this file should remain unchanged. Instead, put your custom logic in `myApp/tasks/`

Now that we are somewhat familiar with the application's structure, we can get it ready to use angular. Open up the `config/routes.js` with your favorite text editor. 
```bash
$ vim config/routes.js
```
You will first notice that sails has great comments at the top of this file that describe its purpose. You will find comments like this in every file! They can be very helpful for understanding how every thing works <font color='green'>✓</font>. Next you will see  line that looks like the following: 
```javascript 
'/': {
    view: 'homepage'
  },
```
This line simply maps the default route of `/` to the homepage view. If you are familiar with express, this should be no surprise. This file gives you a specific place to define your routes.

Now go ahead and open up `views/homepage.ejs`. 
```bash
$ vim views/homepage.ejs
```
This is an example homepage file that could be used as a starting point for your application. The `.ejs` simply stands for embedded javascript and can be used within html to "inject" dynamic data. Because we will be using angular for this, you can just ignore the `ejs` stuff ( anything that looks like <%= %>).  The key takeaway from this is the fact that because we have a route that points to this file, when we do a sails lift and hit the root url, this is the page that will be rendered. Now exit from this file and navigate back to the root directory of your app. 

So we now understand how sails operates as a server-rendered application, but we don't want that. We want Angular! Converting this app from a server-rendered app to a static asset app is as easy as doing the following two steps: 

1. Remove the route from the routes.js file
2. Move the contents in the `views/homepage.ejs` 

We can do this manually, but this is a perfect opportunity to introduce Sails generators. Generators allow us to automatically create or modify existing Sails files from the terminal window.  Sails comes with many preinstalled generators for creating new APIs, models, controllers, etc, but we will install a new one through npm. This is very similar to the Ruby on Rails development process. So without further ado, lets get our new generator.

First use npm to install the sails static generator. 

```bash
$ npm install sails-generate-static --save-dev
```

This will install the static generator and make it a dev dependency in our sails project. Alternatively, you may choose to install this globally if you think you will use it frequently. 

Now from within the root directory of our project run the following command:

```bash
$ sails generate static
```

The static generator simply removed the route from the `routs.js` file and created an `index.html` file in the `assets/` folder. Go ahead and open up the `config/routes.js` and `assets/index.html` files to see what changed. 

Now when we start up our application and hit our root url http://localhost:1337, it will look at our routes file, see there is not routes defined for `/` and search the assets folder instead for index.html. 

We are now ready to use sails for an angular application! 

### Quick Recap 
As a quick recap, we have: 

1. Installed sails
2. Installed a generator
3. Run that generator

We used the following commands to accomplish this:
```bash 
$ sails new testProject
$ cd testProject
$ npm install
$ npm install sails-generate-static --save-dev
$ sails generate static
```

## Install Dependencies

In order to get our Angular app up and running we are going to need some dependencies. We have been using npm for installing everything so far, but we want to separate our frontend and backend dependencies. In order to do so, we are going to use bower for our frontend dependencies. Bower is a widely-used dependency management system that is very similar to npm. To get started we first have to install it using npm. 

Run the following command from within the root directory of the app: 

```bash
$ npm install -g bower
``` 

This will install bower globally. Alternatively, you can save it as a dev-dependency. 

Next we need to set this app up to use bower, by running

```bash
$ bower init
``` 

This will prompt you for some data, you can just accept all of the defaults. When it asks for "what types of modules does this package expose" just select globals. After the setup is complete, run the `ls` command and  you should see a `bower.json`. Now we are ready to start using Bower to install our front end dependencies! 

In order to make a good-looking, well-functioning app, we are going to need Angular, as well as many other front end dependencies. Run the following command from the root directory of our application:  

```bash
$ bower install angular --save
```

This will install angular and place it in your bower_components directory. Much like npm, installing a dependency in this way will install it locally, and save it to the `bower.json`. Note that by default the `.gitignore` file will ignore the `bower_components` directory as well as the node_modules, so you don't have to worry about this! 

We are also going to want to use bootstrap in our app to make things look nice. This is also something that we can install via bower. Note that we may be tempted to install bootstrap, but because we are using angular and don't want to rely on jQuery, we will instead install Angular's version of bootstrap, or ui-bootstrap. 

```bash
$ bower install angular-bootstrap --save
```

And finally we also want to get angular-route, so we can easily configure routes.

```bash
$ bower install angular-route --save
```

## Setup Angular Within Sails

Ok its time to setup your angular application. The beauty here is you can do this however you would like! As long as the files are in the assets directory, you the developer have the freedom to set things up the way you want them. Whether you do this by scratch, or use some sort of generator to scaffold out a basic directory structure is up to you. For the purposes of this introduction, we will be starting it from scratch. 

I like to have all my angular stuff live under a directory called app so we will create a directory under assets called app. From the root directory:

```bash
$ cd assets
$ mkdir app
```

Within the `app` directory, I create two directories, `shared` and `components`. 

```bash
$ cd app
$ mkdir shared
$ mkdir components 
```

And finally I like to put my main module and routes file within `app`. 

```bash
$ touch app.js
$ touch routes.js 
```

For the purposes of this guide, just copy the following content to these new files.

app.js: 
```javascript
angular.module('testProject', ['ngRoute','ui.bootstrap']);
```

routes.js: 
```javascript
angular.module('testProject')
    .config( function($routeProvider){

    $routeProvider
    .when('/', {
      templateUrl: "/app/components/home/home-view.html",
      controller: "HomeController",
      controllerAs: "home"
    });
});
```

At this point your directory structure would look something like the following: 

```bash
-testProject
    -assets
        ...
        -app
            app.js
            routes.js
```

Now, what's an angular application without a view and controller? Let's make one. You can go ahead and do this by hand if you want, but I wrote a cool little sails generator that can do this for me, so I'm going to take advantage of that. First I will install it the same way I did the static generator: 

```bash 
$ npm install sails-generate-angular-controller --save-dev 
```

Now I will use this custom generator to give me a home controller and view. It accepts two arguments, the name of the new controller and the destination directory name `assets/app/components/<direcoryname>` by default. Run the following command from the project's root directory.

```bash
$ sails generate angular-controller home home 
```
If we run the `ls` command on the `assets/app/components/home` directory, you will see a new controller and a new view have been created! 
```
$ ls assets/app/components/home
home-controller.js home-view.html
```

Finally we need to modify the `assets/index.html` file so its an Angular application. If you don't care to keep the helpful comments in the file, replace all contents with the code below. 

```html
<!DOCTYPE html>
<html>
  <head>
    <title>New Sails App</title>
    <!-- Viewport mobile tag for sensible mobile support -->
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1"> 
    <!--STYLES-->
    <!--STYLES END-->
  </head>
  <body ng-app="testProject">
    <div ng-view></div>
    <!--TEMPLATES-->
    <!--TEMPLATES END-->
    <!--SCRIPTS-->
    <!--SCRIPTS END-->
</body>
</html>
```

Otherwise, modify the code by deleting the contents between (not including) the following components: 

```html
<body>
DETETE EVERYTHING IN BETWEEN HERE! 
<!--
  Client-side Templates
  ========================
```

and adding ng-app="testProject" to the `<html>` or `<body>` tag at the top, and and `<div ng-view></div>`. 

Now run a sails lift and see the result... nothing :( . Thats because we never included any of our dependencies in the index.html page. We could do this manually, but Sails will do this for us! Stay tuned for instructions, but first...


### Quick Recap 
As a quick recap, we have: 

1. Installed sails
2. Installed a generator
3. Run that generator
4. Installed Bower
5. Installed some dependencies with bower install
6. Setup a basic directory structure for angular
7. Added a few example files ( some using a generator ) 
8. Created a new index.html with the <ng-app> tag included

We used the following commands to accomplish this:
```bash 
$ sails new testProject
$ cd testProject
$ npm install
$ npm install sails-generate-static --save-dev
$ sails generate static
$ npm install -g bower
$ bower init
$ bower install angular --save
$ bower install angular-bootstrap --save
$ bower install angular-route --save
$ cd assets
$ mkdir app
$ cd app
$ mkdir shared
$ mkdir components 
$ touch app.js
$ touch routes.js 
$ npm install sails-generate-angular-controller --save-dev 
$ sails generate angular-controller home home
```

## Automatically Add Dependencies

Let's fix our index.html page. As previously stated, Sails uses grunt as a build tool. One of the tasks that comes with Sails will take all of the dependencies defined in the `tasks/pipeline.js` file, and inject them into our index page. It does this by looking at the index page and finding the special comments that are seen in the code snippet above. 

If we look at `tasks/pipeline.js`, the stuff defined in the following sections: 
```javascript
// CSS files to inject in order
var  cssFilesToInject = [ 
    'styles/**/*.css'
]
...
// Client-side javascript files to inject in order
var  jsFilesToInject = [ 
    'js/dependencies/sails.io.js',
    'js/dependencies/**/*.js',
    'js/**/*.js'
]

// Client-side HTML templates to inject
var  jsFilesToInject = [ 
    'templates/**/*.html',
]
```

Will be injected into the following sections in the `index.html`: 

```html
<!--STYLES-->
<!--STYLES END-->

<!--SCRIPTS-->
<!--SCRIPTS END-->

<!--TEMPLATES-->
<!--TEMPLATES END-->
```

As you can see, by default nothing in your app folder will be included, so lets fix that! Add the following to the tasks/pipeline.js file. 

```javascript
// Client-side javascript files to inject in order
var  jsFilesToInject = [ 
    
    // ...
    
    // Import all .js files from app ( app.js, routes.js, ... )
    'app/**/*.js'
]
```
Again this is only necessary because the directory stucture of our app differs from the standard structure that sails was expecting (i.e., we placed all of our stuff into the `app` directory, rather than directories for `js`, `styles`, and `templates`). 

Now there is only one more step to get everything up and running. We want to automatically inject our bower dependencies into our index page. Currently we need angular.js, angular-route.js, and ui-bootstrap.js. But they live somewhere in `bower_components`. We could just copy these from `bower_components` to `assets/js/dependencies` and they would get included, but then when we added new dependencies we would have to remember to copy them over as well, which is undesirable. So we are going to add our first grunt task to our application! As previously discussed, grunt tasks are stored in the tasks directory. We are going to be adding a configuration task so lets add our new task in `tasks/config`. Create a new file in that directly called `bower.js` and insert the following code: 

```javascript
/**
 * Install bower components.
 *
 * ---------------------------------------------------------------
 *
 * Installs bower components and copies the required files into the assets folder structure.
 *
 */

module.exports = function(grunt) {

    grunt.config.set('bower', {
        install: {
            options: {
                targetDir: './assets/vendor',
                layout: 'byType',
                install: true,
                verbose: false,
                cleanTargetDir: true,
                cleanBowerDir: false,
                bowerOptions: {}
            }
        }
    });

    grunt.loadNpmTasks('grunt-bower-task');
};

```

This task simply takes your bower components and adds them to an assets/vendor directory at runtime.  In order for this to work, we also need to install the `grunt-bower-task` that this file refers to. Do this by npm installing it as a dependency. 

```bash
$ npm install grunt-bower-task --save
```

If you want more info on this task and what those options do, refer to the following github repo https://github.com/yatskevich/grunt-bower-task. 

Now to make sure this bower task gets run, open up `tasks/register/compileAssets.js` and insert `bower:install` in the array. Your file should look something like this: 

```javascript
module.exports = function (grunt) {
	grunt.registerTask('compileAssets', [
		'bower:install', //<< This is the new line 
		'clean:dev',
		'jst:dev',
		'less:dev',
		'copy:dev',
		'coffee:dev'
	]);
};

```

Lastly we need to point to the vendor directory. We have done this before so nothing here should be unfamiliar. 
Note that we had to make sure angular got loaded before everything else in the vendor directory, so it is important that these lines are in order. Open the `tasks/pipeline.js` file and add the following: 

```javascript

// CSS files to inject in order
var  cssFilesToInject = [ 
    'vendor/**/*.css',//<< load vendor css files
    ...
]
...
// Client-side javascript files to inject in order
var  jsFilesToInject = [ 
    'vendor/angular/angular.js', //<< load angular first
    'vendor/**/*.js'             //<< load other vendor js files 
    ...
]
```

Ok, go ahead and run sails lift and your first sails application is set up and ready to go! 
