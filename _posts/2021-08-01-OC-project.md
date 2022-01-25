---
layout: post
read_time: true
show_date: true
title: "Data mining for catalyst materials"
date: 2021-08-01
img: posts/20210324/starting_adventure.jpg
tags: [general blogging, thoughts, life]
author: Armando Maynez
description: "Midlife career change: a disaster or an opportunity?"
---

## Overview

Catalyst materials are playing important roles in the whole world. Catalysts are composed of different chemical species, leading to the different catalytic performances. While catalysts are often composed of a single kind of metal, they can also be a combination of several or even more than ten kinds of different metals. So the whole possible catalyst space is huge, which means it's extremely hard to directly synthesize each of them and tell which one performs better for a specific case. Thus, machine learning methods step in to help screen the catalysts, but ML methods usually require large data sets for accuracy. Nowadays, many research scientists developed databases for ML usage, but these kind of databases are very large, and include probably all possibly combinations, which may not be the case for a specific catalytic reaction which only needs a small part of this database. So, there is a need to filter the large database to clean the data for a specific problem. 


## Open Catalyst Project data mining

The Open Catalyst Project is a collaborative research effort between Facebook AI Research (FAIR) and Carnegie Mellon University’s (CMU) Department of Chemical Engineering. The aim is to use AI to model and discover new catalysts for use in renewable energy storage to help in addressing climate change.

An open challenge is finding low-cost catalysts to drive these reactions at high rates. Through the use of quantum mechanical simulations (density functional theory), new catalyst structures can be tested and evaluated. Unfortunately, the high computational cost of these simulations limits the number of structures that may be tested. The use of AI or machine learning may provide a method to efficiently approximate these calculations, leading to new approaches in finding effective catalysts.

To enable the broader research community to participate in this important project, we are releasing the Open Catalyst Dataset for training ML models. The dataset contains 1.2 million molecular relaxations with results from over 250 million DFT calculations. In addition to the data, baseline models and code are provided on our Github page. View the leaderboard to see the latest results and to submit your own to the evaluation server! Join the discuss forum to join the discussion with the community and ask any questions.

However, it's not efficient to grab useful data from that huge database, so I set up a couple of codes for quick inquiries. 


### Screen alloys with p-block metals and terminations of (111) and (100)

Import 
```python
import pandas as pd
import pickle
import os
```
Lists of all chemical elements in different blocks. Metals in l2 is the so-called p-block metals.
```python
l1 = ['Pt']
l2 = ['Al','Ga','Ge','In','Sn','Sb','Tl','Pb','Bi','Po']
l3 = ['H','Li','Na','K','Rb','Cs','Fr',
      'Be','Mg','Ca','Sr','Ba','Ra',
      'Sc','Y','Lu','Lr',
      'Ti','Zr','Hf','Rf',
      'V','Nb','Ta','Db',
      'Cr','Mo','W','Sg',
      'Mn','Tc','Re','Bh',
      'Fe','Ru','Os','Hs',
      'Co','Rh','Ir','Mt',
      'Ni','Pd','Ds',
      'Zn','Cd','Hg','Cn',
      'B','Nh',
      'C','Si','Fl',
      'N','As',
      'O','S','Se','Te',
      'F','Cl','Br','I','At','P']
l4 = ['Li','Na','K','Rb','Cs','Fr',
      'Be','Mg','Ca','Sr','Ba','Ra',
      'Nh',
      'Si','Fl',
      'As',
      'Se','Te',
      'Cl','Br','At']
l5 = ['H','P','B','C','N','S','O','F','I']
```
Read in data and filter it.
```python
f1 = pd.read_pickle('../OC.pkl')
dict1 = {}
dict2 = {}
f = {}

j = 0
k = 0

for i,n in enumerate(f1):
    if (f1[n]['miller_index'][0] == 1 and f1[n]['miller_index'][1] == 1 and f1[n]['miller_index'][2] == 1) or (f1[n]['m\
iller_index'][0] == 1 and f1[n]['miller_index'][1] == 0 and f1[n]['miller_index'][2] == 0):
        if any(word in f1[n]['bulk_symbols'] for word in l2):
            if any(word in f1[n]['bulk_symbols'] for word in l4):
                continue
            else:
                #f[n] = {f[n]['bulk_symbols'], f[n]['miller_index']}                                                    
                f[n] = f1[n]
                k += 1
```
Save data. 
```python
if os.path.exists('index.pickle'): os.remove('index.pickle')
if os.path.exists('index.txt'): os.remove('index.txt')

with open('index.pickle', 'wb') as handle:
    pickle.dump(f, handle, protocol=pickle.HIGHEST_PROTOCOL)
with open('index.txt', 'w') as f:
    print(f, file=f)

print ('your job is done, total {} systems'.format(j))
```
Screen all combinations of transition metals and p-block metals.
```python
f = pd.read_pickle('../l4/index.pickle')
dict1 = {}
dict2 = {}

j = 0
k = 0

for i,n in enumerate(f):
    m = 0; p = 0
    for word in l5:
        if word in f[n]['bulk_symbols']:
            string  = f[n]['bulk_symbols']
            results = [i for i in range(len(string)) if string.startswith(word, i)]
            print (results)
            for q in results:
                try:
                    small = f[n]['bulk_symbols'][q+1]
                    #                print (small)                                                                      
                    m += 1
                except:
                    break
		if (word == 'P' and (small in ['t','d','b','o'])) or \
                   (word == 'H' and (small in ['f','s','g'])) or \
                   (word == 'B' and (small in ['a','e','i','r','h'])) or \
                   (word == 'C' and (small in ['a','s','o','l','r','u','d','n'])) or \
                   (word == 'N' and (small in ['a','b','i','h'])) or \
                   (word == 'S' and (small in ['e','n','b','r','c','g','i'])) or \
                   (word == 'O' and small == 's') or \
                   (word == 'F' and (small in ['r','e','l'])) or \
                   (word == 'I' and (small in ['r','n'])):
                    p += 1

    print (f[n]['bulk_symbols'],m,p)
    if m == p:
        dict2[n] = {f[n]['bulk_symbols'], f[n]['miller_index']}
        dict1[n] = f[n]
        j += 1
```
Make sure the materials are bimetallic alloys.
```python
f1 = pd.read_pickle('../T+P/index.pickle')
dict1 = {}
dict2 = {}
f = {}

j = 0
k = 0

for i,n in enumerate(f1):
    r = 0
    for word in l2:
        if word in f1[n]['bulk_symbols']:
            r += 1
    if r == 1:
        f[n] = f1[n]

for i,n in enumerate(f):
    m = 0; p = 0; r = 0
    for word in l3:
        if word in f[n]['bulk_symbols']:
            r += 1
    if r == 1:
        dict2[n] = {f[n]['bulk_symbols'], f[n]['miller_index']}
        dict1[n] = f[n]
        j += 1
```
## Materials Project data mining

By computing properties of all known materials, the Materials Project aims to remove guesswork from materials design in a variety of applications. Experimental research can be targeted to the most promising compounds from computational data sets. Researchers will be able to data-mine scientific trends in materials properties. By providing materials researchers with the information they need to design better, the Materials Project aims to accelerate innovation in materials research.

```python
import pandas as pd
import pickle
import os
from pymatgen import MPRester

data = m.query(criteria={"elements": {"$nin":l6,"$in": l2},"nelements": 2,"spacegroup.symbol":"Pm-3m"}, properties=["material_id","pretty_formula","spacegroup.symbol"])

with open('index', 'w') as txt:
    print(data, file = txt)

data1 = {}
for i,n in enumerate(data):
    if ('3' in data[i]['pretty_formula']) and all(num not in data[i]['pretty_formula'] for num in l_):
#        print (data[i])                                                                                                
        ID = data[i]['material_id']
        data1[ID] = data[i]
        z += 1

with open('index.pickle', 'wb') as handle:
    pickle.dump(data1, handle, protocol=pickle.HIGHEST_PROTOCOL)
with open('index1', 'w') as txt:
    print(data1, file = txt)

print ('your job is done, total {} systems'.format(z))
```

Screen out Pm3m type of crystal structure.
```python
m = MPRester('MlyixfIghRqd56ET')

f = pd.read_pickle('../Bimetallic/index.pickle')

dict1 = {}
dict2 = {}
dict3 = {}

for i,n in enumerate(f):
    ID = f[n]['bulk_mpid']
    if ID in f1:
        if f1[ID]['spacegroup.symbol'] == 'Pm-3m':
            dict3[n] = f[n]
            dict2[n] = {f[n]['bulk_symbols'],f[n]['bulk_mpid'], f[n]['miller_index'],f1[ID]['spacegroup.symbol'],f[n]['ads_symbols']}
            dict1[n] = f[n]
            z += 1

if os.path.exists('index.pickle'): os.remove('index.pickle')
if os.path.exists('index'): os.remove('index')

with open('index.pickle', 'wb') as handle:
    pickle.dump(dict1, handle, protocol=pickle.HIGHEST_PROTOCOL)
with open('index', 'w') as txt:
    print(dict2, file = txt)

print ('your job is done, total {} systems'.format(z))
```

### Try to match the two data sets for A3B type of alloys

```python
m = MPRester('MlyixfIghRqd56ET')

f = pd.read_pickle('../OC.pkl')
f1 = pd.read_pickle('../2e_1/index.pickle')
dict1 = {}
dict2 = {}
dict3 = {}

for i,n in enumerate(f):
    ID = f[n]['bulk_mpid']
    ADS = f[n]['ads_symbols']
    if ID in f1:
        if ADS == '*H':
            if (f[n]['miller_index'][0] == 1 and f[n]['miller_index'][1] == 1 and f[n]['miller_index'][2] == 1) \
            or (f[n]['miller_index'][0] == 1 and f[n]['miller_index'][1] == 0 and f[n]['miller_index'][2] == 0):
                dict2[n] = {f[n]['bulk_symbols'],f[n]['bulk_mpid'],f[n]['miller_index'],
                            f1[ID]['spacegroup.symbol'],f[n]['ads_symbols']}
                dict1[n] = f[n]
                z += 1

if os.path.exists('index.pickle'): os.remove('index.pickle')
if os.path.exists('index'): os.remove('index')

with open('index.pickle', 'wb') as handle:
    pickle.dump(dict1, handle, protocol=pickle.HIGHEST_PROTOCOL)
with open('index', 'w') as txt:
    print(dict2, file = txt)

print ('your job is done, total {} systems'.format(z))
```


## Brief introduction to crystal structures.




With my limited knowledge I have drafted the following learning path:

1. Youtube: [Three Blue One Brown's](https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw) videos on [Neural Networks](https://youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi), [Calculus](https://youtube.com/playlist?list=PLZHQObOWTQDMsr9K-rj53DwVRMYO3t5Yr) and [Linear Algebra](https://youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab). I cannot recommend them enough, they are of sufficient depth and use animation superbly to facilitate the understanding of the subjects.
2. Coursera: [Andrew Ng's Machine Learning course](https://www.coursera.org/learn/machine-learning)
3. Book: [Deep Learning with Python by Francois Chollet](https://www.amazon.com/dp/1617294438/ref=cm_sw_em_r_mt_dp_AV4DHT7CVE95D1SR2JJ8)
4. Book: [Reinforcement Learning: An Introduction, by Richard S. Sutton and Andrew G. Barto](https://www.amazon.com/dp/0262193981/ref=cm_sw_em_r_mt_dp_10B6J4MDB7QE3YHBQF4X)

As for practical work I decided to start by [coding my first models from scratch](./ML-Library-from-scratch.html) (without using libraries such as Tensorflow), to be able to deeply understand the math and logic behind the models, so far it has proven to be priceless.

For my next project I think I will start to do the basic hand-written digits recognition, which is the Machine Learning Hello World, for this I think I will start to use Tensorflow already.

I will continue to write about my learning road, what I find interesting and relevant, and to document all my practical exercises, as well as news and the state of the art in the world of AI.

So far, all I have learned has been so engaging that I am seriously thinking of a career change. I have 17 years of international experience in multinational corporations across various functions, such as Information Services, Sales, Customer Care and New Products Introduction, and sincerely, I am finding more joy in artificial intelligence than anything else I have worked on before. Let's see where the winds take us.

Thanks for reading!

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