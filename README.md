

# Tianyou Mou's Personal Website

### **STEP 4.**
To configure Disqus, set up a [Disqus site](https://disqus.com/admin/create/) with the same name as your site. Then, in `_config.yml`, edit the `disqus_identifier` value to enable.
```YAML
# Disqus
discus_identifier:  # Add your discus identifier
comments_curtain: yes # leave empty to show the disqus embed directly
```
More information on [how to set up Disqus](http://www.perfectlyrandom.org/2014/06/29/adding-disqus-to-your-jekyll-powered-github-pages/).

### **STEP 5.**
Customize the site colors. Modify `/assets/css/main.css` as follows:
```CSS
html {
  --shadow:       rgba(32,30,30,.3);
  --accent:       #DB504A;    /* accent */
  --accent-dark:  #4e3e51;    /* accent 2 (dark) */
  --main:         #326273;    /* main color */
  --main-dim:     #879dab;    /* dimmed version of main color */
  --text:         #201E1E;
  --grey1:        #5F5E58;
  --grey2:        #8D897C;
  --grey3:        #B4B3A7;
  --grey4:        #DAD7D2;
  --grey5:        #F0EFED;
  --background:   #ffffff;
}

html[data-theme="dark"]  {
  --accent:       #d14c47;    /* accent */
  --accent-dark:  #CD8A7A;    /* accent 2 (dark) */
  --main:         #4C6567;    /* main color */
  --main-dim:     #273335;    /* dimmed version of main color */
  --text:         #B4B3A7;
  --grey1:        #8D897C;
  --grey2:        #827F73;
  --grey3:        #76746A;
  --grey4:        #66645D;
  --grey5:        #4A4945;
  --background:   #201E1E;
  --shadow:       rgba(180,179,167,.3);
}
```
### **STEP 6.**
Customize the site fonts. Modify `/assets/css/main.css` as follows:
```CSS
...
  --font1: 'Lora', charter, Georgia, Cambria, 'Times New Roman', Times, serif;/* body text */
  --font2: 'Source Sans Pro', 'Helvetica Neue', Helvetica, Arial, sans-serif; /* headers and titles   */
  --font1-light:      400;
  --font1-regular:    400;
  --font1-bold:       600;
  --font2-light:      200;
  --font2-regular:    400;
  --font2-bold:       700;
...
```
If you change the fonts, you need to also modify `/_includes/head.html` as follows:
Uncomment and change the following line with your new fonts and font weights:
```HTML
<link href="https://fonts.googleapis.com/css?family=Lora:400,600|Source+Sans+Pro:200,400,700" rel="stylesheet">
```
Delete everything within `<style></style>` just before the line above:
```HTML
<style>
/* latin */
@font-face {
  font-family: 'Lora';
  ...
</style>
```

### **STEP 7.**

You will find example posts in your `/_posts/` directory. Go ahead and edit any post and re-build the site to see your changes, for github pages, this happens automatically with every commit. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention of `YYYY-MM-DD-name-of-post.md` and includes the necessary front matter. Take a look at any sample post to get an idea about how it works. If you already have a website built with Jekyll, simply copy over your posts to migrate to Adam Blog 2.0.

The front matter options for each post are:
```YAML
---
layout: post #ensure this one stays like this
read_time: true # calculate and show read time based on number of words
show_date: true # show the date of the post
title:  Your Blog Post Title
date:   XXXX-XX-XX XX:XX:XX XXXX
description: "The description of your blog post"
img: # the path for the hero image, from the image folder (if the image is directly on the image folder, just the filename is needed)
tags: [tags, of, your, post]
author: Your Name
github: username/reponame/ # set this to show a github button on the post
toc: yes # leave empty or erase for no table of contents
---
```
Edit your blogpost using markdown. [Here is a good guide about how to use it.](https://www.markdownguide.org/)

### **STEP 7.**
Delete images inside of ``/assets/img/posts/`` and upload your own images for your posts.

### **STEP 8.**
Make sure Github Pages are turned on in the repository settings, and pointing to the main or master branch (where you cloned this repo).

## Additional documentation

### Directory Structure

If you are familiar with Jekyll, then the Adam Blog 2.0 directory structure shouldn't be too difficult to navigate. The following some highlights of the differences you might notice between the default directory structure. More information on what these folders and files do can be found in the [Jekyll documentation site](https://jekyllrb.com/docs/structure/).

```bash
Adam Blog 2.0/
├── _includes                  # Theme includes
├── _layouts                   # Theme layouts (see below for details)
├── _posts                     # Where all your posts will go
├── assets                     # Style sheets and images are found here
|  ├── css                     # Style sheets go here
|  |  └── _sass                # Folder containing SCSS files
|  |  └── main.css             # Main SCSS file
|  |  └── highlighter          # Style sheet for code syntax highlighting
|  └── img                     # 
|     └── posts                # Images go here
├── _pages                     # Website pages (that are not posts)
├── _config.yml                # Site settings
├── Gemfile                    # Ruby Gemfile for managing Jekyll plugins
├── index.html                 # Home page
├── LICENSE.md                 # License for this theme
├── README.md                  # Includes all of the documentation for this theme
├── feed.xml                   # Generates atom file which Jekyll points to
├── 404.html                   # custom and responsive 404 page
├── all-posts.json             # database of all posts used for infinite scroll
├── ipfs-404.html              # 404 page for IPFS
├── posts-by-tag.json          # database of posts by tag
├── robots.txt                 # SEO crawlers exclusion file
├── search.json                # database of posts used for search
└── sitemap.xml                # automatically generated sitemap for search engines
```

### Starting From Scratch

To completely start from scratch, simply delete all the files in the `_posts`, `assets/img/posts` folders, and add your own content. Everything in the `_config.yml` file can be edited to suit your needs. Also change the `favicon.ico` file to your own favicon.

### Click to tweet

If you have a tweetable quote in your blog post and wish to feature it as a click to tweet block, you just have to use the `<tweet></tweet>` tags, everything between them will be converted in a click to tweet box.

<img width="640px" src="https://github.com/the-mvm/the-mvm.github.io/blob/main/assets/img/template_screenshots/ctt-markdown.png?raw=true">

<img width="640px" src="https://github.com/the-mvm/the-mvm.github.io/blob/main/assets/img/template_screenshots/ctt-render.png?raw=true">

### Google Analytics

It is possible to track your site statistics through [Google Analytics](https://www.google.com/analytics/). Similar to Disqus, you will have to create an account for Google Analytics, and enter the correct Google ID for your site under `google-ID` in the `_config.yml` file. More information on [how to set up Google Analytics](https://michaelsoolee.com/google-analytics-jekyll/).

### Atom Feed

Atom is supported by default through [jekyll-feed](https://github.com/jekyll/jekyll-feed). With jekyll-feed, you can set configuration variables such as 'title', 'description', and 'author', in the `_config.yml` file.

Your atom feed file will be live at `https://your.site/feed.xml` [example](https://the-mvm.github.io/feed.xml).

### Social Media Icons

All social media icons are courtesy of [Font Awesome](http://fontawesome.io/). You can change which icons appear, as well as the account that they link to, in the `_config.yml` file.

### MathJax

Adam Blog 2.0 comes out of the box with [MathJax](https://www.mathjax.org/), which allows you to display mathematical equations in your posts through the use of [LaTeX](http://www.andy-roberts.net/writing/latex/mathematics_1). Just add `Mathjax: yes` in the frontmatter of your post.

```markdown
<p style="text-align:center">
\(\theta_{t+1} = \theta_{t} - \dfrac{\eta}{\sqrt{\hat{v}_t} + \epsilon} \hat{m}_t\).
</p>
```
![rendered mathjax](/assets/img/template_screenshots/MathjaxRendered.jpg)


### Syntax Highlighting

Adam Blog 2.0 provides syntax highlighting through [fenced code blocks](https://help.github.com/articles/creating-and-highlighting-code-blocks/). Syntax highlighting allows you to display source code in different colors and fonts depending on what programming language is being displayed. You can find the full list of supported programming languages [here](https://github.com/jneen/rouge/wiki/List-of-supported-languages-and-lexers). Another option is to embed your code through [Gist](https://en.support.wordpress.com/gist/).

You can choose the color theme for the syntax highlight in the `_config.yml` file:
```YAML
highlight_theme: syntax-base16.monokai.dark # select a theme for the code highlighter
```
See the [highlighter directory](https://github.com/the-mvm/the-mvm.github.io/tree/main/assets/css/highlighter) for reference on the options.

### Markdown

Jekyll offers support for GitHub Flavored Markdown, which allows you to format your posts using the [Markdown syntax](https://guides.github.com/features/mastering-markdown/).

## Everything Else

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/

## Contributing

If you would like to make a feature request, or report a bug or typo in the documentation, then please [submit a GitHub issue](https://github.com/the-mvm/the-mvm.github.io/issues/new). If you would like to make a contribution, then feel free to [submit a pull request](https://help.github.com/articles/about-pull-requests/) - as a bonus, I will credit all contributors below! If this is your first pull request, it may be helpful to read up on the [GitHub Flow](https://guides.github.com/introduction/flow/) first.

Adam Blog 2.0 has been designed as a base for users to customize and fit to their own unique needs. Please keep this in mind when requesting features and/or submitting pull requests. Some examples of changes that I would love to see are things that would make the site easier to use, or better ways of doing things. Please avoid changes that do not benefit the majority of users.

## Questions?

This theme is completely free and open source software. You may use it however you want, as it is distributed under the [MIT License](http://choosealicense.com/licenses/mit/). If you are having any problems, any questions or suggestions, feel free to  [file a GitHub issue](https://github.com/the-mvm/the-mvm.github.io/issues/new).