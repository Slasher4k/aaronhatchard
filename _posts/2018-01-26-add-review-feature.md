---
layout: post
title: "Adding Reviews Part 1"
metadescription: 
---

{::options auto_ids="false" /}

One of the side projects I have been working on lately, and which is still a work in progress, is a website for house sitting in Canada, [canadianhousesits.com][chs]{: .white .white-hover-dots rel="nofollow"}. The site has its basic functionality that allows users to sign up, post house sits and message each other but it doesn't yet have the option for home owners to leave reviews for the house sitters so that is what I am curently adding. As I develop and add this functionality, the steps I take should be useable for any site or app that wants to add a review system, so I will be posting my development process and hope it might be useful for your projects.

When it comes to getting house sitting reviews from a home owner there are two possibilites, first the home owner can choose to leave a review without any prompting or the house sitter needs to request a review from the home owner. If it is the end date for the house sit or later and a home owner is logged in I provide a "Leave Review" button and if a house sitter is logged in they get a "Request Review" button. Now if only it was as easy as adding the buttons, I now have to start thinking about the backend and how I am going to store a review in the database and the rest of the programming logic. In regards to the database design I need to ensure that there can't be more than one review per house sit and that it is linked to a house sitter so that I can display the correct reviews on the house sitters' profile page.

I used a noSQL database for my site, mongoDB, so the database model for my reviews looks like this:

~~~~~
houseSit:{
        type: mongoose.Schema.Types.ObjectId,
        ref: "HouseSit",
        unique: true
},
sitter:{
        type: mongoose.Schema.Types.ObjectId,
        ref: "User"
},
reviewText:{
    type:String,
    default:""
},
published:{
    type:Boolean,
    default:false
},
completed:{
    type:Boolean,
    default:false
}
created:{ type: Date, default: Date.now }
~~~~~
{: style="text-align:left"}

I added the "published" property so the house sitter is able to choose if they want the review to be visible on their public profile and the "completed" property to indicate if the home owner has completed the review.

When it comes to submitting a new review it needs to know which house sit and home owner it is for. For a home owner to to leave a review I need the "Leave Review" button to link to the review form, e.g. canadianhousesits.com/reviews/new, sending the home owner and house sitter information along with the page request, and then create the review once they submit the form. When it comes to a house sitter clicking on "Request Review" it sends an email to the home owner with a link and the link needs to contain information to indicate which house sit and owner it is for. As the review in the database contains a reference to the house sit and the house sitter I will use the review's database id as a token in the link url, so it becomes something like canadianhousesits.com/reviews/new/<review id>, and them use the id to retrieve the entry from the database and extract the needed details. There is still an issue with the link in the email, I can't have a review id without a review in the database, plus I also want the url to the review form to be the same as the one for the "Leave Review" link.

The URL to display the new review form is going to be canadianhousesits.com/reviews/new/<review id> but the actual links for the buttons will be different as I now perform a middle step before the home owener arrives at the page or the house sitter sends the email.



 even though it's the same link I want something different to happen based on whether it's the home owner or house sitter following the link and to do this I have the "Leave Review" as a normal a tag link, which requests the page with a GET request and I have the "Request Review" as the action in a form so that the page is requesting via a POST request thus allowing me to perform a particular action based on the method used to request the page. When either request is made I do some checks to make sure a review doesn't already exist for the house sit, and if one doesn't I create a new review, save it to the database and then uses the new review ID and redirect the home owner to the updated that the home owner is the one that posted that house sit





all that is left to do is notify the house sitter once the review has been left so that they can choose to publish it the their profile




[View finished site][finished_site]{: .accent-inverse .button rel="nofollow"}


[chs]: https://canadianhousesits.com
[finished_site]: https://slasher4k.github.io/fishpond/