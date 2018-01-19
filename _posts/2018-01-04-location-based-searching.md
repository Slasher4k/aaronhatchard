---
layout: post
title: "GeoFire Tutorial"
metadescription: Follow a step by step guide  and learn how to make a simple location based search app using Vuejs, Geofire and firebase.
---

{::options auto_ids="false" /}

Assume you are building an app to rate bars and you store all information for a bar, e.g. name, business hours and price range. Later, you want to add the possibility for users to search for bars in their vicinity. This is where GeoFire comes in. You can store the location for each bar using GeoFire, using the bar IDs as GeoFire keys. GeoFire then allows you to easily query which bar IDs (the keys) are nearby. To display any additional information about the bars, you can load the information for each bar returned by the query.


In this tutorial we are going to use VueJS and Geofire to make an app that allows us to do location based searching, in our case we are going to create an app that searches for fishponds around Grand Falls, Montana, within a radius provided by the user. 


### Prerequisites ###

Firebase database ([follow these instructions][firebase_setup]{: .white .white-hover-dots rel="nofollow"} to setup a database)  
Knowledge of VueJS components, ES6 and Vue-CLI

### Steps ###

Initialise new VueJS project via Vue-CLI and add Firebase and GeoFire to the project

<script src="https://gist.github.com/Slasher4k/f02e651097cc2b122024e827239677ac.js"></script>

Once all the packages have finished installing open the App.vue file and delete everything inside, we will be writing it all from scratch.

First thing we need to do is put our component skeleton code into App.vue, i.e. the template,script and style sections.

<script src="https://gist.github.com/Slasher4k/3a9b131b3a7a1845108fce06394e446c.js"></script>

Now it is time to fill in the code. The heavy lifting for our app is going to be done by GeoFire so the next thing to do is import and setup firebase and GeoFire. 

<script src="https://gist.github.com/Slasher4k/00a3549aa8f392c6bf959db25c6cc4b0.js"></script>

At this point we are technically able to access our location data but we don't have any location data stored in our database, so kind of pointless, to fix that we will now setup our fishpond and location data.

Our fishponds are going to have four pieces of information, name, number of fish, fish species and the ph level and will be stored in an object like so:

~~~~~
{
    name:"Rainbow Pond",
    fishCount:12,
    fishSpecies:["Angel",'Raindow','Trout'],
    phLevel:5
}
~~~~~
{: style="text-align:left"}

In the App.vue data we will add a fishponds property to hold an object with 4 fishponds, each with a unique key i.e. pond1, pond2, pond3 and pond4. 

<script src="https://gist.github.com/Slasher4k/dbce11fda59f2ba018f5a61981c05af1.js"></script>

The fishponds keys are then used as the key values in our GeoFire database, this is important because when a fishpond is found by GeoFire it returns the key value and we then use that key value to find load the appropriate fishpond data to display e.g. if the returned key is "pond2" we can get the corresponding data by using fishponds['pond2'].

To populute the fishpond location database we need to create an array of objects that contain the fishpond key along with its corresponding latitude/longitude location.

~~~~~
[
  {"key":'pond1',"location":[47.611738,-111.206178]},
  {"key":'pond2',"location":[47.486847,-111.359729]},
  {"key":'pond3',"location":[47.381058,-110.944123]},
  {"key":'pond4',"location":[47.413926,-111.297119]}
]
~~~~~ 
{: style="text-align:left"}

Next we need to loop through the location array and add each location to the database, to do this we are going to use one of the code examples provided by GeoFire but slightly modified. We need two files, index.html and fishponds.js, create these in a new folder on your desktop and add the following to each file.

<script src="https://gist.github.com/Slasher4k/e22532c4aa8096ef197e4969049206c9.js"></script>
<script src="https://gist.github.com/Slasher4k/71f7bd0b89e28ba17fff3db99f8e6553.js"></script>


Update index.html with your firebase settings and then open index.html in a web browser and you will see a message once all the fishponds have been added to GeoFire. Once the fishponds are added we can now move onto searching for fishponds near Grand Falls, Montana. To search with GeoFire we use a query object that is provided a location centre, as a latitude/longitude pair, to search around, in this case it's always Grand Falls, and the radius around that point to search within. 

Back in our script section we need to add the remaining properties needed by our app to the data object. The values we need to store are the GeoFire query object, location centre, radius and a list of fishpond ids found by the query.

<script src="https://gist.github.com/Slasher4k/8fce0921e37d79391a77530ad2539226.js"></script>

A radius of 0 isn't going to be very useful so let's go to our template and add an input, linked to radius, so the user can set the radius, and a button, that calls the method "getNearbyPonds" when the user clicks search.

<script src="https://gist.github.com/Slasher4k/ec47e3c841e104f2634ec220858eade6.js"></script>

add the "getNearbyPonds" method to our script section, this method is going to contain all our app logic.

<script src="https://gist.github.com/Slasher4k/664ee927843db2ecf13f146848cf4242.js"></script>

When we call "getNearbyPonds" we need to use a GeoFire query, if one exists we can just update the search options otherwise we need to create one, add to following to the "getNearbyPonds" method.

 <script src="https://gist.github.com/Slasher4k/79bf840d3d469874527034de5f998844.js"></script>

A GeoFire query has two events that we need to listen to, key_entered and key_exited. key_entered is triggered when one of the fishpond locations is found within the search radius and we are given the key of that location, e.g. if the location 47.611738,-111.206178 is within the search radius we get the corresponding key for that location, "pond1", and we can then add it to our list of fishpond ids. key_exited is basically the opposite, when the fishpond is no longer within our search area due to the shrinking of the radius or moving of the search center we get the key of the location that was removed and then we can remove that key from our list of fishpond ids.

In the else statement, after creating the GeoFire query, add our event listeners.

<script src="https://gist.github.com/Slasher4k/bf35f125e11d98437e9c7315e9fd06b7.js"></script>

Now that we can get a list of fishponds within our search area we need to loop through the keys and show the corresponding data, add a fishpond card to our template to show data.

<script src="https://gist.github.com/Slasher4k/a161220c98a0124b6dc2a025fc012949.js"></script>

To finish off add the follow css within the style tags for some basic styling.

<script src="https://gist.github.com/Slasher4k/438c486ed0ca96a1218461a4f083ba14.js"></script>

[View finished site][finished_site]{: .accent-inverse .button rel="nofollow"}


[firebase_setup]: https://firebase.google.com/docs/web/setup
[finished_site]: https://slasher4k.github.io/fishpond/