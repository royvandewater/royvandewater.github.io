---
layout: post
title:  "Forcing Angular directive rendering"
date:   2014-09-15 14:27:00
categories: angular
---
At [Octoblu](https://octoblu.com), we recently ran into an interesting problem with [Angular.JS.](http://angularjs.org)

We have an html panel that contains an Angular Directive. The code looks a little something like this:


{% highlight html %}
<div class="panel-body">
  <custom-directive ng-model="myModel">
</div>
{% endhighlight %}

The issue that we ran in to is this: we swap out the model represented by `myModel` all the time. On initialization, the directive's controller sets up some listeners and fetches some resources from the backend (provided they aren't already cached), and does some other stuff. However, the directive is not re-rendered (and thus controller initialization is not run) each time the model reference changes.

<!--more-->

**Working around this is probably a code smell**. However, we found that adding event listeners and rerunning everything inside the directive's controller was rather ungainly. We also ran into the all-so-common "Every time this model changes and also now" problem, where we set up a bunch of listeners to perform an action when the model changes, but also need to fire it right away on initialization.

So, we decided to trick Angular into re-rending the directive, and here's how:

{% highlight html %}
<div class="panel-body" ng-repeat="model in [myModel]">
  <custom-directive ng-model="model">
</div>
{% endhighlight %}

If you have a better way to accomplish this, or good reasons for not doing this at all, please leave a comment!
