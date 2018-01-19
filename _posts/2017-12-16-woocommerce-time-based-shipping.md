---
layout: post
title: "Woocommece time based shipping plugin"
metadescription: If you want to add a time based shipping calculator to your WooCommerce store then this is the plugin for you
---

<style>
	.wc-list{
		text-align: left;
    	padding-left: 1em;
	}
</style>

{::options auto_ids="false" /}

Lately I have been working on a new Wordpress/Woocommerce site for a client that provides local delivery of their products to their customers, however, the cost of shipping is based on the travel time between their warehouse and the customer's address as determined by Google Maps. Currently their online store doesn't calculate the shipping cost for the items in the customers cart, instead the order is placed without the shipping cost included and is then followed by a multiple step process that involves my client manually extracting the customer's address from the order, entering the address into google maps to get the travel time, using the travel time from Google Maps to calculate the shipping cost and then calling the customer to inform them of the shipping cost and to finalise and orgainse payment for the order. It's clear that the process for calculating the shipping cost is extreme and should be simplified and done in realtime on the website and fortunately moving their online store to Woocommerce it provides the ability to create a custom shipping plugin to calculate shipping costs according to the requirements of the business. With this new ability provided by Woocommerce in hand I set about making a shipping plugin for the client that intergrates Google Maps allowing me to get the travel time to the customer's address, use the returned travel time in the rest of the shipping cost calculation and display the resulting cost to the customer before placing their order. 

Once the plugin was completed the client decided that showing the shipping cost on the site might be a turnoff, they sell construction material of sorts so costs can get relatively expensive, so instead of the plugin going to waste I decided to remove the client specific features and extend the functionality by adding a few other settings to the plugin to make it a more generic plugin that anyone can use to charge shipping costs based on the travel time to the customer. The new version still calculates the travel time to the customer but you can now set how you want to charge for the travel time, i.e. you can charge travel time rounded to the nearest 1, 5, 15, 30 and 60 minute interval and set the price for each interval, plus include a fixed amount to additionally include in the shipping cost. As an example, if you have an order with a travel time of 47 minutes and charged at 5 minute intervals at a rate of $15 per interval you would get a shipping cost of $135 ((47 / 5) X $15). 

The screenshot further down the page shows all the options available in the plugin, however, the important fields within the plugin settings are firstly the origin, the address to calculate shipping from, I would include the street address, city, state, country and postcode for best results, and secondly, and if you want the plugin to work at all, the Google Maps API key. To obtain a Google Maps API key you can follow the instructions [here][gmaps_key]{: .accent .accent-hover-dots rel="nofollow"}. 

<div class="row portfolio-row" style="margin-bottom: 1em;">
    <div class="twelve columns"> 
		<a href="/images/tbs_options.jpg"><img class="u-responsive-img" src="/images/tbs_options.jpg" alt="Time Based Shipping options" style="margin: 0 auto;"></a>
		</div>
	</div>

If you would like to use the time based shipping plugin simply download the file below and then install as follows:
1. Login to your Wordpress admin dashboard
2. Go to Plugins->Add New
3. Choose "Upload Plugin"
4. Click "Choose file" and select the downloaded plugin
5. Click "Install Now"
6. Activate Plugin
{: .wc-list}

The plugin settings are found in Woocommerce->Settings->Shipping->Time Based Shipping


[Download Plugin][download]{: .accent-inverse .button .wrap download="time-based-shipping.zip"} 

[gmaps_key]: https://developers.google.com/maps/documentation/javascript/get-api-key
[download]: /downloads/time-based-shipping.zip