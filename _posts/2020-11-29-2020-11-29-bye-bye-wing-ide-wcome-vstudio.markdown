---
layout: post
title: "Bye Bye Wing Ide, welcome Visual Studio Code"
description: "It's time to change my favourite Python Editor"
date: 2020-11-29 15:02
comments: true
tags: [editor]
categories: [python, osx]
---

Well, i'm not a big Microsoft fan. And more than this i work on OSX and i love it. So during the last 10 years and more, i always used Wing Ide for my projects. 
Wing IDE is just beatiful, nice, strong and full of features. But i alwys try improve my tools, my skill and my knowledgement. So recently i gave an opportunity to Visual Studio Code, and at the end i have to admit that is really comfortable.

I like the numbere of extension available for it. I love pylint, and i love as the UI is designed. More than this the outline, timeline, git integration, compare two file options are really strong features. 

Wing has a great number of features and many of the features of Visual Studio are also available in Wing. But i've to say that in Visual Studio everything looks like more easy to use and well integrated.

There are a couple of things that make me decide for Visual Studio.

1) First of All is Free
2) It's open. I can use it also for PHP and more. For example i'm writing this article using an extension for Makdown Language and another one for Jekyll.

So i've to say bye bye Wing IDE. Was my favourite editor for a decade, but sometime also a big love has to end.

The only think that was hard with Visual Studio was configure the debugger for django and python. There is a tip that i want share with you. The problem was related to the MySQLdb library. The debugger fail with message `django.core.exceptions.ImproperlyConfigured: Error loading MySQLdb module. Did you install mysqlclient?` even if MySQLdb it's installed. The reason is linked to a wrong path inside _mysql.cpython-37-darwin.so.

The only solution i founded was replace the path inside the library as suggested in this [stackoverflow article](https://stackoverflow.com/a/61211114)

So, bye Wing i loved you, but i fall in love with Visual Studio atm

