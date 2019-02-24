---
layout: post
title: "Set sail with Sails.js"
metadescription: "Learn to build web apps quick with the sails.js mvc framework for node.js"
---

{::options auto_ids="false" /}

If you've been using Node.js with Express.js to build your websites then it's time to try Sails.js, which promises to help you "Build practical, production-ready Node.js apps in a matter of weeks, not months.", and who doesn't want to spend less time creating websites. Sails.js is built on top of Express.js and follows the Model-View-Controller pattern.

## Prerequisites
{: .dotted .white-dots}

For this tutorial you will require knowledgede of:

* Javascript
* Node.js 

a knowledge of the following is recommended but not essential:

* Express.js

It is also assumed you already have Node.js installed and you are working on your local machine, not an online development enviroment.

## Getting Started
{: .dotted .white-dots}

The first thing we need to do to get started is install Sails.js and create a demo project. Install sails globally by opening up a terminal and running `npm install sails -g`

With sails now installed globally on your local machine the next thing to do is create the project by typing `sails new <projectName>`, where `<projectName>` is the name of the folder that contains the files for the app, so `sails new blogDemo` will create a folder in your current directory called 'blogDemo' which will contain all the files for the sails application. During the creation of the project you will get a prompt asking you to choose a template for the app, we are starting simple so enter '2' to start with an empty sails app which we will add the functionality to ourselves. Once the application dependacies have finished installing change into the newly created folder and run `sails lift` to start the app and in your browser navigate to `localhost:1337` to see the default homepage.

In this post we are go over routing aka determining what urls our app will respond to and what the user is going to get back. Sails has two types of routes, explicit and implicit also referred to as manual and automaic routing. Explicit urls are defined in the the `config/routes.js` file, and are the ones we will be learing about first, the implicit routes are generated from the models that we use in the app and optionally the functions in our controllers.

## Explicit route syntax
{: .dotted .white-dots}

The syntax of an explicit route is made up of two parts, the address and target. The address is the url entered into the browser and the target, either a string or an object, that points to the code you want to run when that url is visited, usually a method in a controller file. The route syntax looks like the following:

~~~~~
'GET /foo/bar' : 'UserController.subscribe'
^^^^address^^^   ^^^^^^^^^^target^^^^^^^^^^
~~~~~
{: style="text-align:left"}

If we go to our routes file you will notice that firstly the file just exports an object where each key/value pair defines the route with the key being the address and the value being the target. Secondly we already have one explicit url defined for us, the homepage, that we saw earlier which is defined as:

~~~~~
'/': {view: 'pages/homepage'}
~~~~~
{: style="text-align:left"}

Now you might be thinking that looks nothing like the example that was given before, and you would be correct, the address and target also have their own ways of being defined too. In this one `'/'` just means the homepage and view is the HTML template to send back to the user. (we will cover views in a future tutorial)


## Route Address
{: .dotted .white-dots}

The address is just a string and can be broken down into two versions. 

In version one the string contains just the url to a page on the site.

examples: 
~~~~~
'/login'
'/about-us' 
~~~~~
{: style="text-align:left"}

Version two version is more specific and contains two parts seperated with a space. The two parts are the HTTP verb being used to access the route and the url for the route. The HTTP verb allows you to be more restictive about what type of request can access the url e.g. GET, POST, PUT etc. 

examples:
~~~~~
'GET /signup'
'POST /blog/create'
~~~~~
{: style="text-align:left"}

There is the ALL verb which will work for all requests so you can think of version one as a shorthand for the ALL HTTP verb requests, which makes `'ALL /login'` and `'/login'` respond the same way. 

\* All urls in the address must have the '/' at the start to work properly.
{: style="text-align:left; font-weight:bold;margin-bottom:0;"} 
\* The HTTP verbs can be written in either lowercase or uppercase, it is convention to write them in uppercase
{: style="text-align:left; font-weight:bold;"}

To demostrate version two, change the address for `'/'` to `'POST /'`, restart your sails app and go to the homepage.

Even though we saw it earlier you are now told that the page you're trying to reach doesn't exist, this is because when you access a url via the address bar it is sent to the server as a GET request and our homepage is set to only respond to POST requests. 

Set the homepage route to `'GET /'` and restart the app and you can view the homepage again.

If your coming from express this is the equivelent of defining your routes as app.get("/login", user.login) where the method is the HTTP verb, the first parameter is the url part of the address and the second parameter is the target.

For more information on routes read the [official route documentation][routes]{: .white .white-hover-dots rel="nofollow"}.


## Route Targets
{: .dotted .white-dots}

Route targets are the functions that will process, peform some logic and, in the end, respond to the users request. In Sails these target functions are typically refered to as "Actions".

An "Action" can either be a defined as string or an object that tells the app where to find the "action" or it can just be a function expression. There are two types of actions, "Controller actions" that are defined in a controller file and "Standalone actions" which are individual files that export a single function. All actions, no matter which type, are found in the `/api/controllers` folder. Just like express, "Action" functions take the same request and response parameters.

\* Targets are always specified relative to the controllers folder.
{: style="text-align:left; font-weight:bold;"}

### Standalone actions

For a standalone action, the file system structure defines the name of the target. In the `api/controllers` folder create a folder named 'blog' and inside the folder create the file `blog-index.js`, (action file names must be kebab-cased (containing only lowercase letters, numbers and dashes)). Paste the following in the new file:

~~~~~
module.exports = function(req,res){
	res.send('<h1>Welcome to the blog index page</h1>');
}
~~~~~
{: style="text-align:left"}

`blog-index.js` is now a standalone action because it's a file that exports a single function. Because Sails is built on top of express we have all the same response methods available to use so we use res.send to return the html string `<h1>Welcome to the blog index page</h1>`.

Now in the route file we need to define an explicit route to use this action, we will be specific and limit it to GET requests, for this example the address will be `'/blogs'`, for the target we will define it as a string, which is basically the path of the file relative to the controllers folder, so we get the target `'blog/blog-index'`, note that we don't need the .js file extension and the '/' in the string because it's a file path. 

Combining the address and target we end up with the final route of `'GET /blogs' : 'blog/blog-index'`. Add this new route to your routes file and start or restart your Sails app and navigate to `localhost:1337/blogs`, you will see the HTML string returned from the blog-index action displayed on your screen.

We can also use an object to define the action, to do so we need an object with an 'action' key and the same target as it's value, so for our blog index example the object version for the target would be `{action:'blog/blog-index'}` so the route becomes `'GET /blogs' : {action:'blog/blog-index'}`.

The object version can be extend to use a property 'controller' which would be the folders part of the path to the file and the 'action' is just the file.
e.g. `{controller: 'blog', action:'blog-index'}`


### Controller actions 

Controller actions are defined in a file, whose name ends in "Controller" and is PascelCased (every new word starts with a capital letter), which exports an object with  methods that become our "actions".

To learn controller actions we will create a user controller, that has two actions, `registerUser` and `newUser`. `registerUser` will display a simple form for new users to register on the site which we will POST to the `newUser` action, and `newUser` which will display a simple welcome message once the user has registered.

Back in our `api/controllers` folder create the file `UserController.js`, setup our object that will be exported and add the basic skeleton of our methods:

~~~~~
module.exports = {

	registerUser: function(req,res){
        
    },
    newUser: function(req,res){
        
    }
}
~~~~~
{: style="text-align:left"}

To complete 'registerUser' we will send back the following HTML, which simply asks for the user to choose a username:

~~~~~
<h1>Register</h1>
<form action="/register" method="POST">
	<label for="username">Choose Username</label>
	<input type="text" name="username">    
	<input type="submit" value="Register">
</form>
~~~~~
{: style="text-align:left"}

The method attribute of the form tag is "POST" which is the HTTP verb used to send the form to the url in the action attribute, in this case to our new user page.

To complete `newUser` we will just send back a HTML string like we did with the blog-index action that says `<h1>Hi <username>, thank you for registering</h1>`, where `<username>` is replaced with the username entered into the form.

With the completed methods `UserController.js` will look like this:

~~~~~
module.exports = {
    
    registerUser: function(req,res){
        var html = `<h1>Register</h1>
                    <form action="/register" method="post">
                        <label for="username">Choose Username</label>
                        <input type="text" name="username">    
                        <input type="submit" value="Register">
                    </form>`;
        
        res.send(html);
        
        
    },
    newUser: function(req,res){
        var username = req.body.username;
        
        res.send(`<h1>Hi ${username}, thank you for registering</h1>`);
    }
    
}
~~~~~
{: style="text-align:left"}

The last step to get this working is to create the routes to our controller actions. We have two actions so we need two routes, we will create one with the target defined as a string and the other using an object. 

~~~~~
'GET /register':'UserController.registerUser',
'POST /register':{controller:'user', action:"newUser"}
~~~~~
{: style="text-align:left"}

In this example we are using the same url in the address to help illustrate that the HTTP verb is used to determine which action to use. 

One thing to note is that when using a string for the target, unlike standalone actions where we used a forward slash (/), when we use a controller file we use a full stop (.) the same way you would normally access a method on an object.

Just when you think thats all the ways to define targets there is one more method...

### Function target syntax

Using the Function target syntax we assign a route directly to a function

Let's define one last route, a logout route that simply displays "You have been logged out", this time instead of creating any new actions we will use a function as the target value, add the following route to our other routes:

~~~~~
'get /logout': function(req, res){ res.send('<h1>You have been logged out.</h1>')};
~~~~~
{: style="text-align:left"}

Now restart the app and test out our new routes by registering on the site and logging out.


For more information on actions read the [official action documentation][actions]{: .white .white-hover-dots rel="nofollow"}.

## Practice
{: .dotted .white-dots}

Now that you know the basics go ahead and create `deleteUser` and `resetPassword` actions in `UserController.js` and the `show-tags` standalone acton in a 'tag' subfolder of the blog folder that will return some text to the user to let them know what page they are on and then create all the accompanying routes.


## What's next
{: .dotted .white-dots}

In the next tutorial we will be learning all about implicit routes, which means we just need to create actions and Sails will generate the routes based on the methods in our controller files and the structure and nesting of our folders in the `api/controllers` folder.

We will also start talking about data models, which not only get implicit routes, but which also come with implicit actions.

[actions]: https://sailsjs.com/documentation/concepts/actions-and-controllers
[routes]: https://sailsjs.com/documentation/concepts/routes