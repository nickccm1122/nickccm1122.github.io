---
layout: post
title: "Docker+Nginx+Meteor+Prerender"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Recently, I encountered a problem that our wedeal web app has been facing for quite a while - SEO. Since the web is a single page application which is not static which means that it is basically not SEO-friendly. There are some services out there that we could use to enhance our SEO by pre-rendering our web app so that the crawlers can crawl those pre-rendered static htmls instead of fetching simply javascript from our web server. However, in order to make use of the service we need to proxy pass the crawlers’ requests to prerender.io. This can be done in application level but handling that on Nginx level should be more intuitive. This task could be easier if the Nginx and our deployment are independent then we may only need to change the Nginx config and job’s done. However, as we are using meteor-up, a meteor app deployment method that is powered by Docker images, things get a bit more complex.

The Meteor spawns three docker images to handle the Nginx proxy, Letsencrypt renewal process, and the meteor app instance. There is no way to just drop in a block of Ngnix config into anywhere(A location block directive to be specifically) as a variable or template so that the crawlers’ request could be proxy to pretender service. I need to modified the Nginx Proxy docker file to change the original configuration template and rebuild it. I also have to make it public since meteor-up will try to pull it during deployment. Inside the Nginx config file I have to create a [location block][prerender-location-block] that checks the user-agent and proxy_pass to prerender when needed. Even better, a environment variable of prerender token is checked to decide whether pretender location block is used or not. So my own [nginx-proxy][nginx-proxy-prerender] docker is ready to be used in my other meteor-up folk.

For my [meteor-up folk][meteor-up-custom], I need to make use of my own nginx-proxy image and allow myself to pass in Prerender token as a input config. Actually this part was quite daunting to me when I encountered the problem and knowing the need to modified with the project with so much unknown(dockers, meteor-up itself). The fact is that it is not that difficult. Just few lines of changes to let me change the pull location from docker hub and being able to use the token passed in from config file. The deployment will be smoothly done, the prerender service is working and better SEO is coming soon.

![all done][all-done-gif]

Being able to create static html is just the first step towards better SEO for single-page-application. More job need to be done including generating sitemap, creating meta tags. Here is few links that helped in my way of solving the problem:
 * [AngularJS SEO: Get your site indexed and to the top of the search results.](https://prerender.io/js-seo/angularjs-seo-get-your-site-indexed-and-to-the-top-of-the-search-results/)

[nginx-proxy-prerender]: https://github.com/nickccm1122/nginx-proxy
[prerender-location-block]: https://github.com/nickccm1122/nginx-proxy/blob/master/nginx.tmpl#L227
[meteor-up-custom]: https://github.com/nickccm1122/meteor-up
[all-done-gif]: https://media.giphy.com/media/6HOkD1pSCne4E/giphy.gif