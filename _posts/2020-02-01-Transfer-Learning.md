---
layout: post
read_time: true
show_date: true
title: "Predicting material property using transfer learning"
date: 2020-02-01
img: posts/20210324/starting_adventure.jpg
tags: [general blogging, thoughts, life]
author: Armando Maynez
description: "Midlife career change: a disaster or an opportunity?"
---

<center><img src="./assets/img/posts/20200201/TL.png" width="620"></center>

<center><img src="./assets/img/posts/20200201/TLnew1.png" width="620"></center>

<center><img src="./assets/img/posts/20200201/result.png" width="620"></center>

Today I’m going to talk about predicting materials properties using transfer learning

We start from the background.

The evolution of the research workflow in computational chemistry is about 3 generations. 

The first-generation approach is to calculate the physical properties of an input structure, 
which is often performed via an approximation to the Schrödinger equation  For example, Density functional theory.

In the second-generation approach, by using global optimization (for example, an evolutionary algorithm) 
an input of chemical composition is mapped to an output that contains predictions of the structure or ensemble of structures 
that the combination of elements are likely to adopt. 

The emerging third-generation approach is to use machine-learning techniques 
with the ability to predict composition, structure and properties 
But you have to provide sufficient data and an appropriate model.

Also, there are four stages of training a machine-learning model 

Data collection, Representation(like preprocessing), type of learning, Model selection
A very important factor is that you have to provide sufficient amount of accurate data to train your machine learning model. 

Let’s talk about some available databases that we can use for machine learning in our field.
With years of hard work, many DFT computational databases have been setup. 
Most of them are very large. But the problem is most calculations are calculated by basic exchange correlation functionals, like gga. So the accuracy is relative low. 

At the same time, there are some experimental databases with very high accuracy. But since lots of cases you couldn’t really conduct the experiments. So the size of the dataset is limited. 

We can also obtain some other quantum-mechanical databases like coupled cluster quantum method database. Although they have a better accuracy, all of them are computational expensive. 

So, right now we don’t have a proper database with high accuracy and large size. 

We can take a look at some examples.  OQMD is A large dataset containing DFT-computed materials properties for 341K materials
But Thermo-Calc Software is An experimental data set containing only 1963 samples

So, here comes the question: machine learning needs large amount of data to be accurate, 
can we use limited amounts of data to achieve a high accuracy?

There is a Machined Learning framework called “ transfer learning ” which has considerable potential to overcome the problem of limited amounts of data. 

Transfer learning relies on the concept that various property types, such as physical, chemical, electronic, thermodynamics are physically interrelated. 

Previously, if we want to predict a target property with small dataset, we just train this small dataset, although the dataset is accurate, with limited amount of data, we couldn’t get a good accuracy. 
For transfer learning, we can use our exist big dataset to train a model first, and save the common features that are same for different materials, and then use this model to train another small dataset, and then predict a target property with a higher accuracy. 

We know what is transfer learning right row. 

Then, let’s focus on how does this transfer learning work. There are two commonly applied procedures, one is frozen featurizer, another one is called fine-tuning techniques. 

I’m gonna mainly talk about this fine-tuning techniques.

We still have two steps, the first step is using big dataset to pretrain a model. The second step is using this model to predict a target property. 

We first input big data, and here we train this deep neural networks, common features are saved in these layers. 

Then we can use this model, but freeze those layers since we don’t want to lose those common features. We just let the last several layers to be optimized. Then we can get a good prediction of our target property. 

Then, let’s take a look at one example on how to use transfer learning to predict materials properties. 

The input feature here is the element fractions. For example, for OH, fraction of O is 0.5, and fraction of H is also 0.5. 

And the target property is the formation energy of materials.

The pretraining dataset is a DFT dataset with 341K materials. And they trained the model.

Then they used a small database with only 2000 experimental samples to do transfer learning to predict the formation energy. 

Let’s give a look at the training performance. 

First, let’s focus on these two values because both are trained from scratch, which means for both cases, they used a random model first, then input data to train the model and got a performance.  

You can see if they have a large dataset, obviously, they’ll get a lower MAE which means a better performance. But since this OQMD dataset itself is not as accurate as experimental data, so we couldn’t really believe this result. 

Then, let’s compare these two values. The first one is to use the model from large dataset, directly predict the experimental values. The MAE is higher. This transfer learning one is still using the model from large dataset, but do a transfer learning process, basically freeze most layers, retrained last several layers and get a predicting performance. This one is much better. 

So the only difference between them are the OQMD-SC is a direct prediction by the large dataset model, and the transfer learning one has a transfer learning procedure based this large dataset model. 

At last, we can compare these two, we can see with transfer learning applied, we can get a much better performance even though we both just use the same experimental dataset to train the model. That’s because the transfer learning method use the pretrained large dataset  model first, and save the common features in the hidden layers. This helps to improve the performance. 

In summary, A transfer learning model shows an enhanced performance compared with traditional machine learning model 

Also, Transfer learning can be further applied to other area with limited amount of accurate data. 



### P.S. For the geeks like me, here is a snippet on the technical side of the blog.

#### Static Website Generator
I researched a lot on this, when I started I didn't even know I needed a static website generator. I was just sure of one thing, I wanted my blog site to look modern, be easy to update and not to have anything extra or additional content or functionality I did not need.

There is a myriad of website generators nowadays, after a lengthy search the ones I ended up considering are:
- [wordpress](https://wordpress.com/)
- [wix](https://www.wix.com/)
- [squarespace](https://www.squarespace.com/)
- [ghost](https://ghost.org/)
- [webflow](https:webflow.com)
- [netlify](https://www.netlify.com/)
- [hugo](https://gohugo.io/)
- [gatsby](https://www.gatsbyjs.com/docs/glossary/static-site-generator/)
- [jekyll](https://jekyllrb.com/)

I started with the web interfaced generators with included hosting in their offerings:

[wordpress](https://wordpress.com/) is the old standard, it is the one CMS I knew from before, and I thought I needed a fully fledged CMS, so I blindly ran towards it. Turns out, it has grown a lot since I remembered, it is now a fully fledged platform for complex websites and ecommerce development, even so I decided to give it a try, I picked a template and [created a site](https://amaynez.wordpress.com/). Even with the most simplistic and basic template I could find, there is a lot going on in the site. Setting it up was not as difficult or cumbersome as others claim, it took me about one hour to have it up and running, it looks good, but a bit crowded for my personal taste, and I found out it serves ads in your site for the readers, that is a big no for me.

I have tried [wix](https://www.wix.com/) and [squarespace](https://www.squarespace.com/) before, they are fantastic for quick and easy website generation, but their free offering has ads, so again, a big no for me.

I discovered [ghost](https://ghost.org/) as the platform used by one of the bloggers I follow ([Sebastian Ruder](https://ruder.io/)), turns out is a fantastic evolution over wordpress. It runs on the latest technologies, its interface is quite modern, and it is focused on one thing only: publishing. They have a paid hosting service, but the software is open sourced, therefore free to use in any hosting.

I also tested webflow and even created a [mockup](https://armando-maynez.webflow.io) there, the learning curve was quite smooth, and its CMS seems quite robust, but a bit too much for the functionalities I required.

Next were the generators that don't have a web interface, but can be easily set up:

The first I tried was [netlify](https://www.netlify.com/), I also set up a [test site](https://amaynez.netlify.app/) in it. Netlify provides free hosting, and to keep your source files it uses GitHub (a repository keeps the source files where it publishes from). It has its own CMS, Netlify CMS, and you have a choice of site generators: Hugo, Gatsby, MiddleMan, Preact CLI, Next.js, Elevently and Nuxt.js, and once you choose there are some templates for each. I did not find the variety of templates enticing enough, and the set up process was much more cumbersome than with wordpress (at least for my knowledge level). I choose Hugo for my test site.

I also tested [gatsby](https://www.gatsbyjs.com/docs/glossary/static-site-generator/) with it's own Gatsby Cloud hosting service, [here is my test site](https://amaynez.gatsbyjs.io/). They also use GitHub as a base to host the source files to build the website, so you create a repository, and it is connected to it. I found the free template offerings quite limited for what I was looking for.

Finally it came the turn for [jekyll](https://jekyllrb.com/), although an older, and slower generator (compared to Hugo and Gatsby), it was created by one of the founders of GitHub, so it's integration with GitHub Pages is quite natural and painless, so much so, that to use them together you don't even have to install Jekyll in your machine! You have two choices:
1. keep it all online, by having one repository in Github keep all the source files, modify or add them online, and having Jekyll build and publish your site to the special `gh-pages` repository everytime you change or add a new file to the source repository.
2. Have a synchronized local copy of the source files for the website, this way you can edit your blog and customize it in your choice of IDE (Integrated Development Environment). Then, when you update any file on your computer, you just "push" the changes to GitHub, and GitHub Pages automatically uses Jekyll to build and publish your site.

I chose the second option, specially because I can manipulate files, like images, in my laptop, and everytime I sync my local repository with GitHub, they are updated and published automatically. Quite convenient.
   
After testing with several templates to get the feel for it, I decided to keep Jekyll for my blog for several reasons: the convenience of not having to install anything extra on my computer to build my blog, the integration with GitHub Pages, the ease of use, the future proofing via integration with modern technologies such as react or vue and the vast online community that has produced tons of templates and useful information for issue resolution, customization and added functionality.

I picked up a template, just forked the repository and started modifying the files to customize it, it was fast and easy, I even took it upon myself to add some functionality to the template (it served as a coding little project) like:
- SEO meta tags
- Dark mode ([configurable in _config.yml file](https://github.com/the-mvm/the-mvm.github.io/blob/a8d4f781bfbc4107b4842433701d28f5bbf1c520/_config.yml#L10))
- automatic [sitemap.xml](http://the-mvm.github.io/sitemap.xml)
- automatic [archive page](http://the-mvm.github.io/archive/) with infinite scrolling capability
- [new page](https://the-mvm.github.io/tag/?tag=Coding) of posts filtered by a single tag (without needing autopages from paginator V2), also with infinite scrolling
- click to tweet functionality (just add a `<tweet> </tweet>` tag in your markdown.
- custom and responsive [404 page](https://the-mvm.github.io/404.html)
- responsive and automatic Table of Contents (optional per post)
- read time per post automatically calculated
- responsive post tags and social share icons (sticky or inline)
- included linkedin, reddit and bandcamp icons
- *copy link to clipboard* sharing option (and icon)
- view on github link button (optional per post)
- MathJax support (optional per post)
- tag cloud in the home page
- 'back to top' button
- comments 'courtain' to mask the disqus interface until the user clicks on it ([configurable in _config.yml](https://github.com/the-mvm/the-mvm.github.io/blob/d4a67258912e411b639bf5acd470441c4c219544/_config.yml#L13))
- [CSS variables](https://github.com/the-mvm/the-mvm.github.io/blob/d4a67258912e411b639bf5acd470441c4c219544/assets/css/main.css#L8) to make it easy to customize all colors and fonts
- added several pygments themes for code syntax highlight [configurable from the _config.yml file](https://github.com/the-mvm/the-mvm.github.io/blob/e146070e9348c2e8f46cb90e3f0c6eb7b59c041a/_config.yml#L44). See the [highlighter directory](https://github.com/the-mvm/the-mvm.github.io/tree/main/assets/css/highlighter) for reference on the options.
- responsive footer menu and footer logo ([if setup in the config file](https://github.com/the-mvm/the-mvm.github.io/blob/d4a67258912e411b639bf5acd470441c4c219544/_config.yml#L7))
- smoother menu animations 

![my new blog](./assets/img/template_screenshots/homepage-responsive.jpg)

![night theme toggle](./assets/img/template_screenshots/light-toggle.png)

As a summary, Hugo and Gatsby might be much faster than Jekyll to build the sites, but their complexity I think makes them useful for a big site with plenty of posts. For a small site like mine, Jekyll provides sufficient functionality and power without the hassle.

You can use the modified template yourself by [forking my repository](https://github.com/the-mvm/the-mvm.github.io/fork/). Let me know in the comments or feel free to contact me if you are interested in a detailed walkthrough on how to [set it all up](https://github.com/the-mvm/the-mvm.github.io#Installation). 

#### Hosting
Since I decided on Jekyll to generate my site, the choice for hosting was quite obvious, **[Github Pages](https://pages.github.com)** is very nicely integrated with it, it is free, and it has no ads! Plus the domain name isn't too terrible ([the-mvm.github.io](https://the-mvm.github.io)).

##### Interplanetary File System
To contribute to and test [IPFS](https://github.com/ipfs/ipfs#quick-summary) I also set up a [mirror](https://weathered-bread-8229.on.fleek.co/) in IPFS by using [fleek.co](https://fleek.co). I must confess that it was more troublesome than I imagined, it was definetively not plug and play because of the paths used to fetch resources. The nature of IPFS makes short absolute paths for website resources (like images, css and javascript files) inoperative; the easiest fix for this is to use relative paths, however the same relative path that works for the root directory (i.e. `/index.html`) does not work for links inside directories (i.e. `/tags/`), and since the site is static, while generating it, one must make the distinction between the different directory levels for the page to be rendered correctly.

At first I tried a simple (but brute force solution):
{% raw %}
```jekyll
# determine the level of the current file
{% assign lvl = page.url | append:'X' | split:'/' | size %}
# create the relative base (i.e. "../")
{% capture relativebase %}{% for i in (3..lvl) %}../{% endfor %}{% endcapture %}
{% if relativebase == '' %}
	{% assign relativebase = './' %}
{% endif %}
...
# Eliminate unecesary double backslashes
{% capture post_url %}{{ relativebase }}{{ post.url }}{% endcapture %}
{% assign post_url = post_url | replace: "//", "/" %}
```
{% endraw %}
This `jekyll/liquid` code was executed in every page (or include) that needed to reference a resource hosted in the same server.

But this fix did not work for the search function, because it relies on a `search.json` file (also generated programmatically to be served as a static file), therefore when generating this file one either use the relative path for the `root` directory or for a nested directory, thus the search results will only link correctly the corresponding pages if the page where the user searched for something is in the corresponding scope.

So the final solution was to make the whole site flat, meaning to live in a single directory. All pages and posts will live under the root directory, and by doing so, I can control how to address the relative paths for resources.