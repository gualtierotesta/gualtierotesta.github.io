---
author: gualtierotesta
comments: true
date: 2013-04-21 13:34:24+00:00
layout: post
slug: improving-web-pages-1
title: Improving Web pages [1]
wordpress_id: 11
categories:
- HTML
- Javascript
- Web
tags:
- css
- external script
- html
- html page development
- javascript
---

Many Web standards (HTML, CSS, Javascript) allow us to be very free in organizing the files internal structure and their relative position inside our web projects.

Unfortunately this freedom can create situations which are not only difficult to manage in the mid and long terms but also they may work worst than better organized equivalents.

There are few rules which we can follow to improve the quality and, at the same time, the performances of our projects.

The first simple rule is the following:

* Keep Html in .htm / .html files
* Keep CSS rules in .css files
* Keep Javascript code in .js files

Or, in other words, do not embed CSS and Javascript inside HTML (as much as possible, of course).

This strategy creates files with clear meaning and contents.

There are several advantages:

* Pages download is faster because browser can easily cache Javascript and CSS if they are on external (and stable) files. Caching is not possible if Javascript is inside the HTML page.

* Development is easier thanks to the support of HTML editor and IDE like, for example, recent Netbeans 7.3.

* Pages are easier to update, even by other programmer, because of clearer structure.

The internal structure of a HTML page could be like the following

``` html

    <!DOCTYPE html>
    <html>
    <head>
        ....
        <link rel="stylesheet" type="text/css" href="/css/style.css">
    </head>
    <body>
        .... contents....
        <script src="/js/script.js"></script>
    </body>
    </html>
```

In the code above, the CSS rules have been placed in the _style.css_ which is in the _css_ dir. The _css_ dir will contain all our CSS files.

The CSS files should be included with a link tag inside the head tag. Note: the head tag usually contains other information not included in this fragment.

The Javascript code is inside the external _script.js_ file which is in the _js_ dir. The _js_ dir will contains all our Javascript files. Please note that it is not necessary to specify the language or the type in the script tag.

Our project will often use Javascript and CSS files from third party libraries (like jQuery and Bootstrap). In this case it is useful to keep them in a separate dir like `_/ext/<name>`_ where `<name>` is the name of the library. Inside it, the files position is usually defined by the library author.

The target is to keep our project structure clean and clear; the /ext contains everything which has not been created by us.

A final but very important remark: the script tag(s) in a page should be placed at the very **end** of the body tag. Not at body tag begin, nor inside head tag.

Reason is simple: let the browser download all HTML before start to work on Javascript code. The browser can start to create the DOM structure and render (= show to user) the page before executing Javascript code that, in most of the cases, is referring any way to elements in the page. Specifying the Javascript code at the begin of the page is simply delaying rendering phase without any benefit.
