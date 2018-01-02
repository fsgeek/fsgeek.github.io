---
id: 138
title: Where does search functionality live?
date: 2017-05-27T23:05:27+00:00
author: fsgeek
layout: post
guid: https://fsgeek.ca/?p=138
permalink: /2017/05/27/where-does-search-functionality-live/
categories:
  - Semantic File Systems
---
In mulling over the depths of semantic knowledge and file systems, it occurs to me that one thing which differs between the world of Unix/Linux file systems and Windows file systems is that in Unix/Linux environments, search of a directory&#8217;s contents are done in the _shell_ (or application) while in Windows they are a service of the _file system_.

I admit, when I first started working on Windows file systems, I thought this was an annoying decision, since it involved quite a bit of work inside the file system related to string handling and matching.  Even as I write this, I still think that it is a lot of work that really doesn&#8217;t belong in the kernel, but, having said that, this distinction is one reason why a Unix/Linux file systems developer might not think of adding semantic support to a file system as something logical &#8211; after all, the purpose of the file system is to manage storage of file systems and associated meta-data, not to _find things_.  Having experience in the Windows file systems space, I can understand why it might not be a great idea to do this in kernel mode.  After all, C is not a language well-known for its strength and safety in handling strings, and the kernel is not an environment well-known for its tolerance of C runtime error tolerance.

But I digress.  The point is this: when we begin to embed semantic knowledge inside the file system, we exploit a model in which the file system is involved in the search function and this would seem to be anathema to normal file systems behavior.   This is a good challenge: _does this need to be done in the file system_?  If not, perhaps there is instead an abstraction that the file system itself must be able to provide.

Each time I tackle this problem, my general sense is that the model I want is a case in which each file has a set of attributes.  Ideally, what I want is some way to quickly and efficiently find things based upon those attributes.  After all, how hard could this be?

One benefit to the current search paradigm with which users have been trained is that it does not provide reproducible search results.  Thus, nobody will really be surprised if they repeat a search today and get back _different results_ than they got back yesterday.

Hence, I keep coming back to this paradigm.  It also gives me the sense that there are different characteristics of such a system &#8211; there are persistent attributes, like the timestamp, and ephemeral attributes, like semantic tags.

Plenty to think about, but this idea of where to draw the line of search is an important one.  In either case, though, I need to determine efficient ways of rapidly finding files based upon these attributes.