---
title: "Blogging on GitHub Pages"
date: 2017-03-12T22:00:00+08:00
comments: true
---

Last December, GitHub released a new feature that simplifies website or software documentation [publishing with GitHub Pages](https://github.com/blog/2289-publishing-with-github-pages-now-as-easy-as-1-2-3). Not like before, it is no longer solely [powered by Jekyll 3](https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0), but powered by GitHub-flavored [GitHub Pages gem](https://github.com/github/pages-gem). What's more it also comes with [list of beautiful themes](https://github.com/pages-themes).
For step-by-step guide and customization, checkout [this artice](https://help.github.com/articles/creating-a-github-pages-site-with-the-jekyll-theme-chooser/) on GitHub Help.

### Why not just use Jekyll?
Well, what GitHub Pages gem does is _**'to help GitHub Pages users bootstrap and maintain a Jekyll build environment that most closely matches the GitHub pages build environment'**_. As you can see inside the gem's  [dependencies.rb](https://github.com/github/pages-gem/blob/master/lib/github-pages/dependencies.rb) and [plugins.rb](https://github.com/github/pages-gem/blob/master/lib/github-pages/plugins.rb), a list of plugins are included, thus saving much time to look for the right Jekyll plugins. If you just want a publish a simple software documentation site on GitHub, that's more than enough. Most importantly, you could use create a post easily using [GitHub-flavored markdown](https://guides.github.com/features/mastering-markdown/) and publish at your repository on Github.com!

---

## Turn it into a blog
First, before we start the makeover, let's [set up your GitHub Pages site locally](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/).

### Modify CSS & HTML Layout
- [Customizing your Jekyll theme's CSS](https://help.github.com/articles/customizing-css-and-html-in-your-jekyll-theme/#customizing-your-jekyll-themes-css)
- [Customizing your Jekyll theme's HTML layout](https://help.github.com/articles/customizing-css-and-html-in-your-jekyll-theme/#customizing-your-jekyll-themes-html-layout)

### Create New Layout
A blog might not only contains blog posts, but also static pages like 'about.html'. Since it is visually different from a blog post, it's a good idea to have a different layout for it.

1. Copy `/_layouts/default.html` from your Jekyll theme's source repository ([see this](https://help.github.com/articles/customizing-css-and-html-in-your-jekyll-theme/#customizing-your-jekyll-themes-html-layout))
1. Duplicate and rename as `/_layouts/page.html`
1. Customize the layout as you'd like
1. To apply the new 'page' layout on a page, remember to include the respected [Front Matter](https://jekyllrb.com/docs/frontmatter/) variables on top of the file.
For example, here is how to apply 'page' layout on `/index.md `

```markdown
---
layout: page
---
## Welcome to My Blog!
```

## Create a blog post
Since GitHub Pages gem is powered by Jekyll, we could mirror its [directory structure](https://jekyllrb.com/docs/structure/) and make our site works like a blog. Though it's not strictly enforced, [Jekyll suggests blog post files to be named in such format](https://jekyllrb.com/docs/posts/#creating-post-files), `YEAR-MONTH-DAY-title.MARKUP` and would return the post link as such `http:YOUR-GITHUB-URL/:year/:month/:day/:title.html` by default. Of course, you could always [configure permalink format](https://jekyllrb.com/docs/permalinks/#where-to-configure-permalinks).

1. Create a subfolder `/_posts` at top level folder
1. Create a new `.MD` file `/_posts/2017-03-11-hello-world.md`
1. Write some contents on the post
1. Visit the blog post at http:://YOUR-GITHUB-URL/2017/03/11/hello-world.html

### Displaying List of Blog Posts
Now you have all posts in a folder, maybe it's a good idea to create an index of posts on a page. It can be easily be done using [Liquid Template Language](https://docs.shopify.com/themes/liquid/basics). See [this page](https://jekyllrb.com/docs/templates/) to learn more about how template works. Here's a basic example of how to create list of links to your blog posts.

{% raw  %}
```html
<ul>
  {% for post in site.posts %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
```
{% endraw %}

### Create Error 404 Not Found Page
1. Create a `.MD` file `404.md` or HTML file `404.html` at top level folder
1. Design the error page
1. Done!

## Discussion section
Since Jekyll handles only static pages, we must find other ways to make the discussion section. That's where [Disqus](https://disqus.com) comes into place. Follow Disqus' [Quick Start Guide](https://help.disqus.com/customer/portal/articles/466182-quick-start-guide) to embed JavaScript code into your preferred layout.

### :bulb: D.R.Y (Don't Repeat Yourself)
Use `include` tag to include content from another file stored in `/_includes` folder. For example, you could create `/_includes/footer.html` and include it into all the layouts. So that you won't have to modify every layout for a minor change on footer section.
{% raw %}
```liquid
{% include footer.html %}
```
{% endraw %}

### Miscellaneous
- [Adding Jekyll plugins to a GitHub Pages site](https://help.github.com/articles/adding-jekyll-plugins-to-a-github-pages-site/)
- [Jekyll::Gist - Displaying GitHub Gist on Jekyll Pages](https://github.com/jekyll/jekyll-gist)
- [Emoji Cheat Sheet](https://www.webpagefx.com/tools/emoji-cheat-sheet/)
