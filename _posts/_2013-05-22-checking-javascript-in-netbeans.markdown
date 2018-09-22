---
author: gualtierotesta
comments: true
date: 2013-05-22 20:00:10+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/05/22/checking-javascript-in-netbeans/
slug: checking-javascript-in-netbeans
title: Checking JavaScript in NetBeans
wordpress_id: 47
categories:
- Javascript
- Netbeans
tags:
- code checks
- coding
- external plugin
- javascript
- jslint
- netbeans
- plugin
- quality checks
---

NetBeans 7.3 has introduced an improved JavaScript support, including a wider support for JavaScript code checks. It is also possible to install a JSLint plugin for stronger code checking.

Let's start with NetBeans native capabilities. In the Tools -> Options -> Editor -> Hints we can find all NetBeans code quality checks, divided by language.

The following picture shows the JavaScript checks:

[![2013-05-22_fig1](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig1.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig1.png)

In the following picture we see how the NetBeans JavaScript hints work:

[![2013-05-22_fig2](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig2.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig2.png)

This is nice feature to have so we can fix the errors while we are coding. Unfortunately the NetBeans JavaScript checks are limited (in number) and we should use an external plugin to enforce our code checking rules to our js files.

[JSLint](http://www.jslint.com/) is the reference JavaScript quality checker. A good JSLint plugin for NetBeans is available [here](http://plugins.netbeans.org/plugin/40893/jslint).

To install it in NetBeans, juts follow this simple procedure:

1. Download the plugin on the PC

2. Open the Plugin window in NetBeans (Tools -> Plugins) and click on the "Downloaded" tab.

3. Click on "Add Plugins" and select the downloaded file.

[![2013-05-22_fig3](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig3.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig3.png)

4. Press "Install" to start JSLint plugin installation.

After installation, we can go back to our file and check it now with JSLint. Open the context menu (right mouse click) and select "JSLint". In the Action Items window, we find all errors detected by JSLint on our file.

[![2013-05-22_fig4](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig4.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig4.png)

As we can see in the picture, JSLint is more restrictive than NetBeans, showing more errors (3 in our small example). For the complete and reference list of JSLint checks please refer to the JSLint author [page](http://www.jslint.com/lint.html).

If the Action Items list contains errors on other files in the project and in particular on libraries files (like jQuery and Bootstrap js files) we can easily filter out them. We can create an Action Item Filter as shown in the following images:

[![2013-05-22_fig5](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig5.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig5.png)

[![2013-05-22_fig6](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig6.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig6.png)

JSLint plugin configuration can be found in Tools -> Options -> Miscellaneous -> JSLint (tab). The options set on this page are valid for all JavaScript files in the project. In the next picture, the configuration I usually use in my projects:

[![2013-05-22_fig7](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig7.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-22_fig7.png)

Basically I limit line length to 80 characters and define _window_ as global variable.

File specific settings can be defined as follows:

[code language="javascript"]
/*jslint sloppy:true */
/*global window */
function myFunc(txt) {
window.alert(txt)
}
```

The_ /*jslint_ contains JSLint configuration parameters while_ /*global_ defines implicit global variables (defined outside the current file). **Important**: there should be no space between the asterisk and the words _jslint_ and _global_ so take care that NetBeans formatting will not try to format Javascript comments.
