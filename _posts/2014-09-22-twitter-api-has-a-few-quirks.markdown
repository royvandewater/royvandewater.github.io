---
layout: post
title:  "The Twitter API has a few quirks"
date:   2014-09-15 14:27:00
categories: api, twitter
---

At [Octoblu](https://octoblu.com), we pride ourselves in supporting as much of our built-in API's as possible. However, we had some serious issues getting [Twitter's API](https://dev.twitter.com/) to function the way we'd expect.

### POST Content-type encoding

Most modern REST API's will take a look at your request's `Content-type` header. If the `Content-type` is declared as `application/json`, then the server expects your post body to be JSON. If the `Content-type` is `x-www-form-urlencoded`, then the body should be form and url encoded. Twitter, however, **will only accept x-www-form-urlencoded post bodies**. Your `Content-type` will be ignored, and you will get a very unhelpful HTTP 403 status code back with an empty body. Unfortunately, this means that you will have to form and url encode your POST and PUT requests, which leads us into the next quirk.

<!--more-->

### URL encoding form parameters

The standard Javascript library provides us an excellent function for URL encoding our parameter keys and values, `encodeURIComponent`. However, there's just one tiny problem. The standard library function encodes a whole suite of characters, with a few notable exceptions. Specifically, it leaves out `[!,',(,),*]`. This is because most servers don't care, they know what you mean when you send a * or ! character, but Twitter doesn't. To deal with this issue, the [MDN encodeURIComponent documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent) provides a fixed encodeURIComponent.

{% highlight javascript %}
function fixedEncodeURIComponent (str) {
  return encodeURIComponent(str).replace(/[!'()]/g, escape).replace(/\*/g, "%2A");
}
{% endhighlight %}

Not doing this will result in an just as helpful of an error as JSON encoding the body, a 401 HTTP status code with an body that states "Error: Unauthorized".

Make sure to account for these two quirks, and your experience with Twitter will be much better.

One more thing. While trying to fix this issue, we ran across a number of posts that claimed you have to alphabetically sort your params by their key. We experimented with this and found we could post them up in any order.
