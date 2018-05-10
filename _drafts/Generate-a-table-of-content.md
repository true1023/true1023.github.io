---
layout: post
title: Generate a table of content
tags: [toc.js, kramdown, Markdown, Customization]
---

Test article, get the source on [github](https://github.com/Sylhare/Type-on-Strap/blob/gh-pages/_posts/2013-12-12-toc.js-for-table-of-content.md).

# Using Kramdown GFM

<!-- To be placed at the beginning of the post, it is where the table of content will be generated -->
* TOC
{:toc}

## Basic Usage


You need to put this at the beginning of the page where you want the table of content to be displayed

```html
* TOC
{:toc}
```

It will then render the markdown and html titles (lines that begins with `#` or using the `<h1></h1>` tages)

# Using toc.js

Demo display of [jekyll-table-of-contents](https://github.com/ghiculescu/jekyll-table-of-contents) by ghiculescu.


## Customize with toc.js

[toc.js](https://github.com/ghiculescu/jekyll-table-of-contents) stands for table of content, it is a js plugin that generates automatically a table of content of a post.

### Use with this jekyll template

If you want to customize the theme it is up to you, you can add the `toc.js` file into the `asset > js` and add it into the `page.html` layout with:

```html
<script src="{{ site.baseurl }}/assets/js/toc.js" ></script>
```
Then you can use it as it is said on the repository.

## Basic Usage

The script requires jQuery. First, reference toc.js in templates where you would like to add the table of content. Then, create an HTML element wherever you want your table of contents to appear:

```html
<div id="toc"></div>
```

Then you put your post with titles and all like:

```apiblueprint
## Title
## Mid title 1
This is text on page one
## Mid title 2
This is text for page two
### Sub title 2.a
Some more text
```

Then at the end of your post, you call the .toc() function using:

```html
<script type="text/javascript">
$(document).ready(function() {
    $('#toc').toc();
});
</script>
```

## How it would look like

![image](https://user-images.githubusercontent.com/20642750/39189661-c22099f2-47a0-11e8-826e-2ec3ef4cc4f4.png)

<!-- To be copied at the end of the post to render the table of content -->
<script>
$(document).ready(function() {
    $('#toc').toc();
});
</script>