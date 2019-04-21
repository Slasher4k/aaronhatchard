---
layout: post
title: "WP Scene Builder"
metadescription: "WordPress plugin to create a scene by applying multiple layers over the background to flesh out a scene"
---


I recently had an enquiry to make something like this [bathroom fitter][bathConfig]{: .white .white-hover-dots rel="nofollow"}, it basically has a background image of a bathroom and then you can select from various options for bathtubs, tiles, faucets, soap dishes etc that are then overlaid on top of the background image to create a mockup of what a bathroom could look like with the chosen configuration. I decided to generalise the idea and make a WordPress plugin that provides the ability to create multiple scenes, with as many layers as you would like, which can be shown on any page via a shortcode and also provides the ability for the user to have a PDF file emailed to them containing the scene that they have built.  

## About the Plugin
{: .dotted .white-dots}

The plugin adds three new post types to WordPress, these are "SB Scenes", "SB Layers" and "SB Overlays". A "SB Scene" sets the background image and a collection of "SB Layers", an "SB Layer" has a layer name and a collection of "SB Overlays" and an "SB Overlay" has the overlay image, a thumbnail version for the overlay and the overlay name which is shown as the thumbnail label. The easiest way to setup a scene is to begin with adding the overlays first, followed by creating the layers and assign the required overlays and then creating the scene and choosing it's layers. 

When the plugin's shortcode is added to a page it outputs the 'Scene Builder' interface onto the page, this interface contains the "Scene Panel", which contains the scene background image and any selected overlays, the "Overlay Selector Panel" which shows the layer assigned to the scene with their accompanying overlay options, and lastly, if enable in the options, the "PDF Request Form" which allows a user to request a PDF containing the scene which they have built. 

On mobile devices the "Scene Panel" and "Overlay Panel" and "PDF Request Form" are displayed stacked on top of each other, on larger devices the "Scene Panel" and "Overlay Panel" are shown side by side and the "Overlay Panel" becomes scrollable if it's content is taller than the "Scene Panel".

I have setup a beach scene demo consisting of 9 overlays divided between 3 layers, sky, ocean and sand.

[![Scene builder user interface][builder]{: .u-responsive-img}][builder]

[View the demo][demo]{: .button .accent-inverse}


## Plugin Options
{: .dotted .white-dots}

The plugin comes with a fairly comprehensive options page to adjust all aspects of the scene builder. There are the Layout options for defining the mobile breakpoint and maximum width of the scene builder. You have the ability to set the maximum width of the "Scene Panel" and the "Overlay Selector Panel" and if the total width is less than the size of browser window you can choose if you want it positioned on the left, centered or on the right.

When it comes to the "Overlay Selector Panel" you can set the width of the thumbnails, the spacing around them, whether to show the thumbnail name, the image to use for the 'clear' option and full configuration options for the styling of the font used for the layer name and thumbnail labels.

For the "PDF Request Form" you can choose to show or hide the feature, you have full control over the styling of the text, the button background and text colours, the wording of all the elements in the form like the input label, placeholder and submit button text, as well as the email subject and body.

One other option is the ability to either support Internet Explorer or not, it is an older browser and if you choose not to support it it reduces the files required by the plugin by one. There is a feature in newer browsers that Internet Explorer doesn't support natively so an additional script is loaded to provide the missing functionality.

For best results the scene image and the overlays should all be the same dimension 

## Plugin Helpers
{: .dotted .white-dots}

To provide all the plugin features I make use of a few third party libraries. 

Having new post types alone wasn't going to provide the ability to obtain the informational requirements to create the scene builder so [Advanced Custom Fields][acf]{: .white .white-hover-dots rel="nofollow"} is used to provide the new post types with the necessary fields to gather the data.

If you've ever tried to make your own custom options page you know how tedious and time consuming that is, to save me time and provide the user with an easy to use options panel I have used the [Redux Framework][redux]{: .white .white-hover-dots rel="nofollow"}.

The last feature that was needed was the creation of a PDF to send as an email attachment, this feature was implemented with the help of the [FPDF library][fpdf]{: .white .white-hover-dots rel="nofollow"}.

## Plugin Optimisations
{: .dotted .white-dots}

In designing this plugin there were a few things I wanted to be mindful of, firstly the scene builder is going to potentially contain a lot of images so I want it to have as little impact on the initial page load speed as possible, secondly, I want to whole interface to be shown after the scene background image and the thumbnails have all been loaded instead of having individual images pop into the page as they finish loading and lastly I don't want my plugin to have an effect on pages where the plugin is not used.



## Loading the images
{: .dotted .white-dots}

When a website is being displayed in a browser and the site contains images the browser requests the images on the page from the server, as soon as it comes across it in the HTML, and only once all the images, and other requested resources, have been download is the page deemed to be loaded. To prevent the loading of lots of images from slowing down the loading of the page the thumbnail and layer overlay images needed for the scene builder are not contained in the HTML and are requested via a script after the page has finished loading. Due to the scene (background) image generally being the biggest image in regards to file size it is requested first before the page has finished loading, however, it is requested asynchronously as not to impede the loading of the page and only once it is loaded is it added to the page. In the end the images are requested in order of use, the background image first, followed by the thumbnail images and then the overlays which are only needed after a thumbnail image has been clicked.

## Showing the scene
{: .dotted .white-dots}

Now that all the images that are needed are being loaded asynchronously via a script we can track their loaded status and only show the scene builder once all the images have loaded. To allow for this the initial state of the scene builder is to show a loading spinner while in the background the images are being loaded and then switching to the full scene builder once image loading has complete. It is only the scene background and the thumbnails that have their loading status tracked as they are the only ones needed for the main scene builder interface.

## Encapsulation
{: .dotted .white-dots}

When building a plugin that requires additional CSS and Javascript it isn't uncommon for the plugin to include these files on all pages once the plugin is activated even if the page isn't using the plugins features and functionality. The scene builder only requires two Javascript files which are only added to a page if it contains the scene builders shortcode, these files have also been minified to be the smallest size possible, and the CSS needed is also only output as part of the HTML generated by the shortcode, this ensures the pages that don't include the shortcode don't have any unnecessary Javascript or CSS loaded.

## The End
{: .dotted .white-dots}

This was a fun plugin to make, I had the chance to use a the FPDF library which I hadn't used before, always good learning new things as once you know it you can incorporate it into future projects. The plugin has been submitted to WordPress for inclusion in their plugin directory, if it is accepted you will be free to use it on your site and will be my first plugin publicly available from WordPress.

This initial version of the plugin only scratches the surface of possibilities that could be provided, it could be update to included interchangeable background images in a scene or allow a user to upload their own background image from their computer, useful if the scene builder is used to mockup bedroom furniture or the like. In the realm of e-commerce each overlay could have an associated price/product id and be used as price estimator e.g. you could have a background image of a mannequin and overlay clothing items. 

If you would like a customised version of this plugin, a custom plugin in general or need any other help with your website then contact me to discuss your requirements.

[Contact Me](/contact){: .accent-inverse .button}


[bathConfig]: https://www.bathfitter.com/design-your-own-bathroom
[demo]: https://jellyrobot.com.au/thebeach
[acf]: https://www.advancedcustomfields.com
[fpdf]: http://www.fpdf.org
[redux]: https://redux.io/
[builder]: \images\scene_builder.png