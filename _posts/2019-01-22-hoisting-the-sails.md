---
layout: post
title: "Sails.js implicit routing"
metadescription: "Learn how to setup implicit routing using the Sails.js Realtime MVC Framework for Node.js"
---

{::options auto_ids="false" /}


## Prerequisites
{: .dotted .white-dots}

For this tutorial you will require knowledgede of:

* Javascript
* Express.js  

As we will be building on the code from the previous tutorial I highly recommend that you read part one of this series [here][partone]{: .white .white-hover-dots target="_blank" }.

## Enabling implict routes
{: .dotted .white-dots}

We have so far seen that to respond to a request to a page on our site e.g. `/register`, we needed to explicitly declare the route and the action that will process the request and send a response to the user i.e. `"GET /register": 'UserController.register'`.

Sails.js allows us to just create our actions and it will automatically generate routes to them for use, to do this we just need to change one setting, in the `config/blueprints.js` file and we need to enable "actions" but uncommenting the line `// actions: false` changing it to `actions: true`.

## Setting up routes
{: .dotted .white-dots}

To access our actions the route becomes, for standalone actions, the path to and the name of the action. Create a standalone action called 'topics' in our tag folder.

In the topics action we will just output a H1 containing "Maths, Science, Computing" i.e. `res.send("<h1>Maths, Science, Computing</h1>")`. The path to this action is `/blog/tag`, all paths are relative to the api folder, and the name of the action file is `topics.js` so our route will be `/blog/tag/topics`.

Start your sails app with `sails lift` and naviage to `localhost:4000/blog/tag/topics` and you will see the html that we sent back and we didn't need to add the route to the route.js file, too easy.

For controller files the route becomes the name of the controller, so for UserController it is just `/user` and then the name of the function in the file. In our UserController add the method 'userDetails' which returns the string `<h1>My name is Simon and I am 30</h1>`.

Once that is done we can visit  the route by going to `/user/userdetails` and we see our h1.

Now that we can have routes generated automatically for us what happens to actions that already have a route assigned to them like registerUser in the UserController? We have an explicit route for the action `registerUser` from the `UserController.js` file assigned to the route `/register` but if we go to `/user/registerUser` we will see we get the same thing as if we went to `/register`, so even though we have an explicit route for an action the implicit controller/action url version is still created. 

If we want to define a url that doesn't contain the controller and action name, like `/register` instead of `/user/registerUser`, we need to define them explicitly.

Be mindful of the names you give your actions when using implicit routes as the names that we use for the actions will become part of the url.

## Implicit route HTTP Verbs
{: .dotted .white-dots}

Implicit routes respond to HTTP Verbs GET, POST, PUT, PATCH and DELETE, because of this fact if you want to use the same url for different request types e.g GET and POST then you need to check the request method using req.method and then choose how to repsond to the request.

In our userDetails action we will add a check, if it is a GET request we will display a form to the user asking for a name and age, if it is POST we will change the string that is sent back to include the new details from the form.

~~~~~
userDetails(req,res){
        
    if (req.method === 'GET') {
        var html = `<h1>User Details</h1>
                    <form action="/user/userdetails" method="post">
                        <label for="name">Name</label>
                        <input type="text" name="name">  
                        <label for="age">Age</label>
                        <input type="text" name="age"> 
                        <input type="submit" value="Submit">
                    </form>`;        
        res.send(html);
    } else if (req.method === 'POST') {         
        res.send(`<h1>My name is ${req.body.name} and I am ${req.body.age}</h1>`)
    }
}

~~~~~
{: style="text-align:left"}

The disadvantage of this is that the actions are not as lightweight as they could be as they need to contain all the logic for various requests types where as if we use explicit routes we can have seperate actions that only deal with a specific request type.

## Route matching
{: .dotted .white-dots}

When sails is looking for a matching route it first searches the explicit routes in config/routes.js then searches the implicit routes, due to this behaviour it is possible for us to override an implicit route url and use a different action with it. This is helpful if we still want to split up the logic for say GET and POST requests while still using the same implicit URL.

To demonstate this we will override the implicit GET url for our 'userDetails' action.

First create a userDetailsForm method in the UserController and copy the GET logic from the userDetails method into it:

~~~~~
userDetailsForm(req,res){
    var html = `<h1>User Details</h1>
        <form action="/user/userdetails" method="post">
            <label for="name">Name</label>
            <input type="text" name="name">  
            <label for="age">Age</label>
            <input type="text" name="age"> 
            <input type="submit" value="Submit">
        </form>`;
        
        res.send(html);        
    }
~~~~~
{: style="text-align:left"}

next update the userDetails action to just use the POST logic:

~~~~~
userDetails(req,res){
    retrun res.send(`<h1>My name is ${req.body.name} and I am ${req.body.age}</h1>`);
}
~~~~~
{: style="text-align:left"}

now we need to create the explicit GET version of the implicit userdetails route and have it go to the userDetailsForm action: 

`'GET /user/userdetails': 'UserController.userDetailsForm'`

and finally restart our app and naviagate to `/user/userdetails`. You will be presented with the form from the newly overridden implicit route. Fill in the form and submit it and you will see the string returned from the implicit POST route.

If we don't want to use one of the HTTP verbs of an implicit route we can set an explicit route for it and set the target as {response: 'notFound'} e.g. `'POST /user/registerUser':{response: 'notFound'}` or if we want to disable the route completely we can use `'/user/registerUser': {response: 'notFound'}`

## What's next
{: .dotted .white-dots}

In the next tutorial we will start looking at how to store and retreive data with sails.js

[partone]: https://www.aaronhatchard.com.au/2018/09/14/set-sail-with-sails-js.html