---
author: gualtierotesta
comments: true
date: 2013-05-13 19:00:52+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/05/13/how-to-define-variables-in-javascript/
slug: how-to-define-variables-in-javascript
title: How to define variables in Javascript
wordpress_id: 35
categories:
- Javascript
- Web
tags:
- coding
- javascript
- jshint
- jslint
---

As in every language, also in Javascript there are several different ways to define variables and everybody is free to select the preferred approach. Nevertheless we should consider the following:



	
  * Javascript has some unusual rules on variables scoping

	
  * Code quality checkers like [JSLint](http://www.jslint.com/) and [JSHint](http://www.jshint.com/) prefer some specific solutions

	
  * Using a "standard" solution makes our code cleaner and more readable


The approach I use is quite standard and it can be summarized in few rules.

The basic rule is that all variables should be defined before their use. Javascript does not force us to define the variables before usage; if not defined, Javascript will create them on the fly.

Unfortunately this behavior let us to make errors like this:

[code language="javascript"]
activeList = true;
if (activList) {
alert("The list is active");
}
```

Inside the if the variable name is wrong; in the example Javascript will create a new variable with false as default value so the alert will never be invoked.

If possible, variables should be defined inside functions.

Global variables (defined in the same .js file but outside any function) should be used only if there are no alternatives. These kind of variables should be defined at the begon of the file and with a relevant comment.

External variables (defined in other .js files) should not be used (even if this is not always possible).

The _var_ instruction should be the first in the function body

Example:

[code language="javascript"]
var gl = 1;  // global var
function myFunc () {
    var lc1 = 0,
        lc2 = 15,
        i;
    for (i = gl; i < 10; i += 1) {
        lc1 = lc1 + i;
    }
    ....
}
```


Notes:



	
  * _gl_ is a global variable. It is defined and commented at the the begin of the file.

	
  * _lc1_, _lc2_ and _i_ are all local variables, defined inside the _myFunc_ function.

	
  * I have used only one var statement for all the local variables. This is also the preferred JSLint solution. It is also acceptable to use one var instruction for each variable. Here what it is important is to be coherent and to use the same solution among all project files.

	
  * For a similar reason I have defined the loop variable (i) together with the others. Alternative could be to define it inside the loop instruction (for (var i = gl; ....) but we should not forget that index variables have the same scope of the other function variables and they are not limited to the loop scope (like, for example, in Java)



