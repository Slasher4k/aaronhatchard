---
layout: post
title: "New Website Design"
---

{::options auto_ids="false" /}

Welcome to my new and improved website.

My original site was just a single page WordPress website using a fairly basic template that I made specifically for it, however, just like my phone, after about a year it was time for an upgrade. I still liked the general design of my site but to become more popular with search engines \*cough\* Google \*cough\*, and in turn real people, it was time to add more pages and a blog (so I can tell you all about my web dev adventures). 

When it came time to update the design the first thing to change was the font family, which went from a combination of 'Share Tech Mono' and 'Roboto Slab' to just 'Montserrat'. The fonts were closely followed by tweaks made to the colors, I stuck with the same colors, orange, grey and white, however the orange was pretty intense so that was tamed down and I added blue for the buttons to help them stand out from the crowd. My design crosshairs then moved on to their next target, which happens to appear on every page, the navigation menu. The original site's navigation menu consisted of just five links, styled like buttons, that took you to different parts of the page, the new site was going to have seven, and coming from a family of seven I know how crowded that would make things. To reduce the space taken up be each navigation link the links went from looking like buttons with an icon and text to plain text with an underline if it's the currently active page. The cherry on top of all the changes though, and to add some fun to the new design, was to be the dotted underlining of the heading of each unique section on the page.


After the design was taken care of I decided that I wanted to try, and learn, something new, so instead of sticking with WordPress I made a switch and developed this site with [Jeykll][jekyll]{: .white .white-hover-dots rel="nofollow"}, a blog-aware static site generator. Jekyll is similiar to WordPress in that each page of the website can be constructed from a collection of templates parts (header, content and footer) that are then combined to make a single page, unlike WordPress though, Jekyll does not require the use of a database for the blog, each post is just a text file in a folder named posts.  To keep the load time fast and page sizes small I also opted to avoid the jQuery library and use plain javascript.

[jekyll]: https://jekyllrb.com/ 