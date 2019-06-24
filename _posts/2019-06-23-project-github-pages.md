---
layout: default
permalink: /p/github-pages
title: Personal Website - chrisweaver1.github.io
description: My personal website, built using Jekyll, Bootstrap and hosted on Github Pages.
catagories: ['project']
customcss: /assets/css/markdown.css
image: /assets/images/icons/octojekyll.png
---

## Personal Website - [chrisweaver1.github.io](https://chrisweaver1.github.io)

[![Generic badge](https://img.shields.io/badge/Github-chrisweaver1.github.io-blue.svg)](https://github.com/ChrisWeaver1/chrisweaver1.github.io)

My personal website, built using [Jekyll](https://jekyllrb.com/), [Bootstrap](https://getbootstrap.com) and hosted on [Github Pages](https://pages.github.com/). I've made a number of personal sites over the past few years and I always disliked them in one way or another, this iteration seems a bit different. Even though I havn't used Ruby before, Jekyll has been pleasently suprising with the amount of customisation and tools it provides, especially for creating a personal blog or website. Paired with Github Pages for free hosting and easy deployment, it has been a pleasent experience to use. 

Jekyll provides support for a number of well known technologies like Markdown and Liquid, making it really easy to pick up. Having markdown support makes it easy to write new content for the website and the implementation here is really nice as we can provide variables, layouts, tags and descriptions in the front matter, we can also include html and use Liquid inside the markdown content. [Liquid](https://github.com/Shopify/liquid), paired with Jekyll, gives you more utilities for building webpages. You can reuse html with includes, add if statements and for loops, aswell as access a number of variables provided by Jekyll to format your pages. I've not _yet_ had to write any additional Javascript because there is usually another solution provided through Jekyll. The directory structure of the project is rather specific, but it is easy and well documented over at [Jekyll](https://jekyllrb.com/).

Their support for blog posts has also been extremly nice, being able to add tags and catagories on each post, aswell a global `site.posts` variable, which is an array of all of your posts and their details, displaying them is really easy. You can select from a number of themes if you aren't too great at design (like myself) or go your own way. Github Pages also makes life easy, just setup the repository and push your jekyll project to master to deploy your site.

#### Development

Basic overview of development setup for a project like this. See [Bootstraps Blog](https://github.com/twbs/bootstrap-blog) or [Twitter Open Source](https://github.com/twitter/twitter.github.io) for reference projects.

See my project here: [chrisweaver1/chrisweaver1.github.io](https://github.com/ChrisWeaver1/chrisweaver1.github.io)

1. Setup a reposiroty, calling it `<username>.github.io` for Github Pages to pick it up without further configuration
1. [Development environment & project setup](https://jekyllrb.com/docs/)
1. Add `gem 'github-pages', group: :jekyll_plugins` to the Gemfile
1. Run `gem install bundler`
1. Test using `bundle exec jekyll serve`
1. Deploy by pushing to master 

#### Project Structure

|---|---|
| Structure | Use |
|---|---|
| _includes/ | Directory of reusable html for use on different pages|
| _layouts/ | Contains base html layouts, pages can extend these layouts. Which layout to use can be defined in the front matter |
| _posts/ | All of your 'post' pages, these have special properties over other pages |
| _site/ | Build directory, Jekyll builds your site into a static site and serves to files from here |
| _config.yml | Global configuration settings, you can define most of this per page in their front matter aswell |
| *.html, *.md | If they have a front matter, Jekyll will build these files and copy the built files to _site with the same directory structure |
| other | If it's not excluded in the _config.yml, jekyll will pull through everything else as static pages i.e. assets/, index.html and so on |
