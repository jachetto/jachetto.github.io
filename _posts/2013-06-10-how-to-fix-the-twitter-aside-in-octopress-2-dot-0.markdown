---
layout: post
title: "How to fix the Twitter Aside in Octopress 2.0"
date: 2013-06-10 20:13
comments: true
categories: Octopress Twitter
keywords: Twitter Aside API
---

<span style="color: red">UPDATE: This article is deprecated. Twitter has retired the API 1. With API 1.1. it's mandatory an Oauth Auth, so the better way is build a server side proxy to get the json data.</span>

So, i'm really a newbye with octopress, i've started to use it more or less 2 month ago, but i really like it. It's fast, it's simple, it's easy, the only thing that i disliked when i started to use it is that the Twitter Aside does not worked for me, it hangs on "Twitter's busted" and nothing happens after this.

So i managed to understand where was the problem and i discovered the issue in getTwitterFeed function in source/javascripts/twitter.js

This is the original code:

{% codeblock source/javascripts/twitter.js lang:javascript %}
function getTwitterFeed(user, count, replies) {
  count = parseInt(count, 10);
  $.ajax({
      url: "https://api.twitter.com/1/statuses/user_timeline/" + user + ".json?trim_user=true&count=" + (count + 20) + "&include_entities=1&exclude_replies=" + (replies ? "0" : "1") + "&callback=?"
    , type: 'jsonp'
    , error: function (err) { $('#tweets li.loading').addClass('error').text("Twitter's busted"); }
    , success: function(data) { showTwitterFeed(data.slice(0, count), user); }
  })
}
{% endcodeblock %}

and this is how i managed to make it working

{% codeblock source/javascripts/twitter.js lang:javascript %}
function getTwitterFeed(user, count, replies) {
  count = parseInt(count, 10);
  $.ajax({
      url: "http://api.twitter.com/1/statuses/user_timeline/" + user + ".json?trim_user=true&count=" + (count + 20) + "&include_entities=1&exclude_replies=" + (replies ? "0" : "1") + "&callback=?"
    , dataType: 'jsonp'
    , error: function (err) { $('#tweets li.loading').addClass('error').text("Twitter's busted"); }
    , success: function(data) { showTwitterFeed(data.slice(0, count), user); }
  })
}
{% endcodeblock %}

It works! so now i'm an happy octopress twittered blogger 
