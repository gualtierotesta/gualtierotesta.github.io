---
author: gualtierotesta
comments: true
date: 2013-06-25 19:00:21+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/06/25/utility-functions-in-javascript/
slug: utility-functions-in-javascript
title: Utility functions in JavaScript
wordpress_id: 106
categories:
- Javascript
tags:
- coding
- external script
- html page development
- javascript
- jslint
---

We often have JavaScript functions which are used in several pages of our project. Best approach is to store all these functions in a shared JavaScript file, included when necessary.

The usual approach I adopt (and suggest) is to create an utility class like the following one:

[code language="javascript"]
function Util () {}

Util.read = function (sel) {
    return jQuery(sel).val();
};

Util.write = function (sel, val) {
    jQuery(sel).val(val);
};
```

We start to define an empty Class (named Util) by a constructor and then we add to it our functions. 
These are "static" like functions which are always available, without the need to create an instance of the class Util (with the new operator).

I save the above code in a util.js file (file name = class name).

How to use the utility functions ?

[code language="javascript"]
    // read the value of the element with id myinput
    var val = Util.read('#myinput');
```

JSLint users: don't forget to add Util in the globals list  ( /*globals Util */ )

Final comment: these approach should be used only for utility methods which do not save internal state
or use global variables. The constructor function should be empty; the functions defined as above (and not via class prototype) cannot access to any
class field.
