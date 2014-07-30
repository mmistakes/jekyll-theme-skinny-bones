---
layout: article
title: "Getting Started with Skinny Bones"
date: 2014-06-25T13:57:25-04:00
modified:
excerpt:
tags: []
image:
  feature:
  teaser:
  thumb:
toc: true
share: false
---

## Installation

Skinny Bones requires [Jekyll](http://jekyllrb.com/) 2+. Make sure to run `gem update jekyll` if you aren't on the latest version or `gem install jekyll` if this is your first time installing it.

If you are creating a new Jekyll site using Skinny Bones following these steps:

1. [Download Skinny Bones](https://github.com/mmistakes/skinny-bones-jekyll/archive/master.zip) and unzip.
2. Rename `skinny-bones-jekyll-master` to something meaningful ie: `new-site`
3. Run `bundle install` to install all dependencies (Jekyll, [Jekyll-Sitemap](https://github.com/jekyll/jekyll-sitemap), [Octopress](https://github.com/octopress/octopress), [Bourbon](http://bourbon.io), etc)
4. Update `config.yml`, add navigation, and add posts/pages. Full details below.

If you want to use Skinny Bones with an existing Jekyll site follow these steps:

1. [Download Skinny Bones](https://github.com/mmistakes/skinny-bones-jekyll/archive/master.zip) and unzip.
2. Rename `skinny-bones-jekyll-master` to something meaningful ie: `new-site`
3. Run `bundle install` to install all dependencies (Jekyll, [Jekyll-Sitemap](https://github.com/jekyll/jekyll-sitemap), [Octopress](https://github.com/octopress/octopress), [Bourbon](http://bourbon.io), etc)
4. Add all of your existing posts, pages, and any other content you want to move over.
5. Update posts and pages YAML to match names used by Skinny Bones. Full details below.
6. Update `config.yml`, add navigation links. Full details below. 

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

### Site Wide Configuration

`_config.yml` is your friend. Open it up and personalize it. Most variables are self explanatory but here's an explanation of each if needed:

#### `title`

The title of your site... shocker!

Example `title: Skinny Bones`

#### `description`

A description of your site. Used in page meta.

Example `description: "A lightweight site starter for Jekyll"`

#### `logo`

Site logo, used mostly as a default image that appears on Twitter Cards if a large feature image isn't present. If used place a square image around 120x120 px in `/images/`.

Example `logo: 120x120.gif`

#### `teaser`

Default teaser image used for posts and pages that do not have one assigned. This image shows up in the grid archive listing module. If can have whatever dimensions you want, I've found that an image with rectangular proportions work nicely. Like the site logo, place in `/images/`.

Example `teaser: 400x250.gif`

#### `url`

Used to generate absolute urls in `sitemap.xml`, `atom.xml`, and for generating canonical URLs in `<head>`. When developing locally either comment this out or use something like `http://localhost:4000` so all assets load properly.

Examples:

{% highlight yaml %}
url: http://mmistakes.github.io/skinny-bones-jekyll
url: http://localhost:4000
url: http://mademistakes.com
url: 
{% endhighlight %}

### Navigation Links

To set what links appear in the top navigation edit `_data/navigation.yml`. Use the following format to set the URL, title, teaser image/description (used in sliding menu) of each link:

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

### Posts

#### Organizing Posts

Create new .md files in `_posts`. If you aren't using categories to organize your posts then don't worry about this next bit. I like to organize all my posts by a single category and file them under sub-directories beneath `_posts`. For example if I have post named `2014-06-01-new-post.md` and it has a category of `foo` it will have the following path:

`_posts/foo/2014-06-01-new-post.md`

Which when process by jekyll would have a URL of `domain.com/foo/new-post/`.

#### Post YAML

The only YAML required is `title` and `layout`. It's a good idea to add a custom excerpt otherwise Jekyll will just take the content from the first sentence. Images are a good idea as well to add visual interest to a page.

1. Add `categories: ` and assign a single category name for added organization
2. image YAML for featured images, teasers, and square thumbnails
3. Table of Contents conditional
4. Ads conditional (Google Responsive Ads `_includes/advertising.html`)
5. Disqus comments conditional 

### Pages

#### Organizing Pages

Pages can be handled in two ways if you want to maintain pretty URLs for your site.

Place `.md` files in root and add the appropriate permalink YAML. For example if you want your **About** page to live at `domain.com/about/` create a file named `/about.md` and add `permalink: /about/` to its YAML.

Or you can create `/about/index.md` and omit the YAML permalink. The choice is yours.

### Archives

Create an archive page for each category or section of your site that you want to list a collection of posts. On these pages you'll want to use the `archive` layout and use a variation of the following for the page's content changing `foo` to the appropriate category you want to list posts from.

{% highlight html %}
<div class="tiles">
{{ "{% for post in site.categories.foo " }}%}
  {{ "{% include post-grid.html " }}%}
{{ "{% endfor " }}%}
</div><!-- /.tiles -->
{% endhighlight %}

If you'd prefer a less visual list of posts use `{% include post-list.html %}` instead of the `post-grid.html` _include.

---

## Adding New Content

Install the [Octopress](https://github.com/octopress/octopress) gem if it isn't already.

{% highlight bash %}
$ gem install octopress --pre
{% endhighlight %}

### New Post

Default command

{% highlight bash %}
$ octopress new post "Post Title"
{% endhighlight %}

Default works great if you want all your posts in one directory, but if you're like me and want to group them into subfolders like `/articles`, `/portfolio`, etc. Then this is the command for you. By specifying the DIR it will create a new post in that folder and populate the `categories:` YAML with the same value.

{% highlight bash %}
{% endhighlight %}

The default `_layout` used for new posts is `articles`. If you want to use the media layout or something else specify it like so

{% highlight bash %}
$ octopress new post "Portfolio Post Title" --dir portfolio --template media
{% endhighlight %}

### New Page

To create a new page use the following command.

{% highlight bash %}
$ octopress new page about/
{% endhighlight %}

This will create a page at `/about/index.md`

---

## Contributing

Found a bug or aren't quite sure how something works? By all means [submit an issue](https://github.com/mmistakes/skinny-bones-jekyll/issues) on GitHub. For straight forward bug fixes feel free to submit pull requests. 

Skinny Bones was built predominately as a framework for [my own site](http://mademistakes.com). My intention was never to make it all things for all people. So please keep that in mind before submitting new features or enhancements. If you've used any of my [Jekyll themes](http://mademistakes.com/work/jekyll-themes/) I think it's pretty clear I favor the simple over the complex.