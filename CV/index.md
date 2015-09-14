---
layout: article
title: "My Academic Merits"
date: 2014-06-25T13:57:25-04:00
modified:
excerpt:
tags: []
image:
  feature:
  teaser:
  thumb:
share: false
---


{% include toc.html %}

## EDUCATION

> * 2013-2014	Further training (selected): Social Network Analysis, Max Planck Institute for the Studies of Societies, Germany; Multilevel analysis at the Centre for Multilevel Modelling, University of Bristol, the UK; and R-programming at Karolinska Institute, Stockholm, Sweden. 
> * 2011-2012	Pedagogical Training for Teaching in Higher Education, (various certificates), University of Gothenburg
> * 2011	PhD. in Sociology, Department of Sociology, University of Gothenburg, Gothenburg, Sweden.
> * 2006	M.A. in Sociology. Minor in: Political Science, Economics, and Mathematics. University of Gothenburg, & University of Jönköping, Sweden.  


---

## ACADEMIC POSITIONS
### Appointments
* 2013, Sept. – 2016, Sept.	International Post-Doc, Department of Sociology, University of Gothenburg, Gothenburg, Sweden.  
* 2011, May – 2013, Aug.	Lecturer/Researcher, Department of Sociology, University of Gothenburg, Gothenburg, Sweden.
* 2006, Mar. – 2011, Apr.	Doctoral student, Department of Sociology, University of Gothenburg, Gothenburg, Sweden.  

### Visiting positions
* 2015, Sept. – 2016, Apr. 	Fielding School of Public Health, University of California, Los Angeles, the United States. 
* 2015, Feb. – 2015, Sept.	Department of Economics, The New School for Social Research, New York, the United States. 
* 2013, Sept. – 2015, Jan.	The Max Planck Institute for the Studies of Societies, Cologne, Germany. 
*2 012, Mar. – 2012, Apr.	Townsend Center for International Poverty Research, University of Bristol, the UK. 
* 2010, Feb. – 2010, Apr	Faculty of Economics, University of Cambridge, the UK. 


{% highlight text %}
bundle exec jekyll build

bundle exec jekyll serve
{% endhighlight %}

---

## Scaffolding

How Skinny Bones is organized and what the various files are. All posts, layouts, includes, stylesheets, assets, and whatever else is grouped nicely under the root folder. The compiled Jekyll site outputs to `_site/`.

{% highlight bash %}
skinny-bones-jekyll-master
├── _site                               # compiled site ready to deploy
├── _images                             # unoptimized images
├── _includes                           # reusable blocks for _layouts
├── _layouts
|    ├── archive.html                   # archive listing of a group of posts or collection
|    ├── article.html                   # articles, blog posts, text heavy material layout
|    ├── default.html                   # base
|    ├── home.html                      # home page
|    └── media.html                     # portfolio, work, media layout
├── _posts                              # posts grouped by category for sanity
├── _sass
|   ├── vendor                          
|   |   ├── bourbon                     # Bourbon mixin library   
|   |   └── neat                        # Neat grid library
|   ├── _animations.scss                # CSS3 animations
|   ├── _badges.scss                    # small badges
|   ├── _bullets.scss                   # visual bullets
|   ├── _buttons.scss                   # buttons
|   ├── _grid-settings.scss             # Neat settings         
|   ├── _helpers.scss                   # site wide helper classes
|   ├── _layout.scss                    # structure and placement, the bulk of the design
|   ├── _mixins.scss                    # custom mixins
|   ├── _notices.scss                   # notice blocks
|   ├── _pygments.scss                  # Pygments.rb syntax highlighting
|   ├── _reset.scss                     # normalize and reset elements
|   ├── _sliding-menu.scss              # sliding menu overlay
|   ├── _variables.scss                 # global colors and fonts
|   ├── css
|   └── main.scss                       # loads all Sass partials
├── fonts                               # webfonts
├── images                              # images
├── js
|   ├── plugins                         # jQuery plugins
|   ├── vendor                          # vendor scripts that don't get combined with the rest
|   ├── _main.js                        # site scripts and plugin settings go here
|   └── main.min.js                     # concatenated and minified site scripts
├── apple-touch-icon-precomposed.png    # 152x152 px for iOS
├── atom.xml                            # posts feed
├── favicon.ico                         # 32x32 px for browsers
└── index.md                            # homepage content
└── _config.yml                         # Jekyll settings
{% endhighlight %}

---

## Site Setup

A quick checklist of the files you'll want to edit to get up and running.

---

### Site Wide Configuration

`_config.yml` is your friend. Open it up and personalize it. Most variables are self explanatory but here's an explanation of each anyways:

#### Site Title

The title of your site... shocker!

Example `title: Skinny Bones`

#### Site Description

A description of your site. Used in page meta.

Example `description: "A lightweight site starter for Jekyll"`

#### Site Logo

Site logo, used mostly as a default image that appears on Twitter Cards if a large feature image isn't present. If used place a square image around 120 x 120 pixels in `/images/`.

Example `logo: 120x120.gif`

<figure>
  <img src="{{ site.url }}/images/twitter-card-screenshot.jpg" alt="sample Twitter Card screenshot">
  <figcaption>Site logo is used for Twitter Cards when a feature image is not defined for the page.</figcaption>
</figure>

#### Site Teaser Image

Default teaser image used for posts and pages that do not have one assigned. This image shows up in the archive grid module. If can have whatever dimensions you want, I've found that an image with rectangular proportions work nicely. Like the site logo, place in `/images/`.

Example `teaser: 400x250.gif`

#### Site URL

Used to generate absolute URLs in `sitemap.xml`, `atom.xml`, and for generating canonical URLs in `<head>`. When developing locally either comment this out or use something like `http://localhost:4000` so all assets load properly. *Don't include a trailing `/`*. [Protocol-relative URLs](http://www.paulirish.com/2010/the-protocol-relative-url/) are a nice option but there are a few caveats[^protocol].

Examples:

{% highlight yaml %}
url: http://mmistakes.github.io/skinny-bones-jekyll
url: http://localhost:4000
url: //cooldude.github.io
url: 
{% endhighlight %}

[^protocol]: If you decide to use a protocol-relative URL know that it will most likely break sitemap.xml that the Jekyll-Sitemap gem creates. If a valid sitemap matters to you I'd suggest [creating your own sitemap.xml](http://davidensinger.com/2013/03/generating-a-sitemap-in-jekyll-without-a-plugin/) and apply some Liquid logic to prepend links to posts/pages with `https:`.

---

### Navigation Links

To set what links appear in the top navigation edit `_data/navigation.yml`. Use the following format to set the URL, title, teaser image/description (used in off-canvas menu) of each link:

{% highlight yaml %}
- title: Portfolio
  url: /portfolio/
  excerpt: "Things I’ve designed, illustrated, developed, coded, and whatever."
  image: 300x200.gif

- title: Made Mistakes
  url: http://mademistakes.com  
{% endhighlight %}

To set what links appear in the footer edit `_data/footer.yml`. Use the following format for each link:

{% highlight yaml %}
- title: Subscribe
  url: /subscribe/

- title: External Page
  url: http://mademistakes.com  
{% endhighlight %}

---

### Posts

#### Organizing Posts

Create new MarkDown (.md) files in `_posts`. If you aren't using categories to organize your posts then don't worry about this next bit. I like to organize all my posts by a single category and file them under sub-directories beneath `_posts`. For example if I have post named `2014-06-01-new-post.md` and it has a category of `foo` it will have the following path:

`_posts/foo/2014-06-01-new-post.md`

Which when process by jekyll would have a URL of `domain.com/foo/new-post/`.

---

### YAML Front Matter

The only YAML Front Matter required for posts and pages are `title` and `layout`, everything else is optional.

#### Categories

Add `categories: ` and assign a single category name for added organization. The theme makes the assumption that posts will only be assigned to one category at a time. You can certainly assign multiple categories but unexpected things may happen with the permalink structure.

I like to group posts by general categories like `articles`, `portfolio`, `whatever`, and using tags to more specific classifications.

#### Modified Date

If you assign a modified date to a post or page it will override the published date and appear in the page footer with the following format `Updated August 27, 2014`. It's also used as metadata to give search engines another piece of info about your content.

I like to use this [Sublime Text plugin](https://github.com/FichteFoll/sublimetext-insertdate) to insert the current date after I've updated a post, but you can do it manually to. It should follow the same date format used by Jekyll when naming posts: `YYYY-MM-DD`.

{% highlight YAML %}
modified: 2014-08-27
modified: 2014-08-27T11:57:41-04:00 # more verbose, also acceptable
{% endhighlight %}

#### Images

Here you can define the various images assigned to posts and pages. 

##### Featured Images

A good rule of thumb is to keep feature images nice and wide so you don't push the body text too far down. An image cropped around around 1024 x 256 pixels will keep file size down with an acceptable resolution for most devices without bloating your site. If you want to level-up your site I'd suggest looking at the [Jekyll Picture Tag](https://github.com/robwierzbowski/jekyll-picture-tag) plugin[^plugins] to make them responsive.

[^plugins]: If you're using GitHub Pages to host your site be aware that plugins are disabled. You'll need to build your site locally and then manually deploy if you want to use this sweet plugin.

The post and page layouts make the assumption that the feature images live in the `images/` folder. To add a feature image to a post or page just include the filename in the front matter like so.

{% highlight yaml %}
image:
  feature: feature-image-filename.jpg
{% endhighlight %}

To add attribution to a feature image use the following YAML front matter on posts or pages. Image credits appear directly below the feature image with a link back to the original source (if supplied).

{% highlight yaml %}
image:
  feature: feature-image-filename.jpg
  credit: Michael Rose #name of the person or site you want to credit
  creditlink: http://mademistakes.com #url to their site or licensing
{% endhighlight %}

##### Teasers Images


