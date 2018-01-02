---
id: 202
title: Of file handles and implicit offsets (Follow-up)
date: 2017-12-02T17:00:35+00:00
author: fsgeek
layout: post
guid: https://fsgeek.ca/?p=202
permalink: /2017/12/02/of-file-handles-and-implicit-offsets-follow-up/
categories:
  - Questions
---
My post about [file handles](https://fsgeek.ca/2017/11/25/of-file-handles-and-implicit-offsets/) elicited some interesting feedback, so I wanted to capture it because I thought it provided some insight.

Shared libraries were not a standard part of UNIX systems in the 1980s (though they had certainly been described in prior work) and thus one interesting observation here is that putting code **in the kernel** was a way of minimizing the amplification of common runtime code. The use of shared libraries today and our increased certainty that kernels need to be as small as we can make them certainly would lead to a very different divide today.

Conversations with Malcolm are often insightful, so I wanted to capture it here because it is _definitely_ germane to the area that I&#8217;m exploring, particularly as I try to explain some of the underlying rationale for it &#8211; a combination of software archaeology and pragmatically looking at how to evolve forward.

> I always thought this was just a programming convenience, because it allows a simple program to have &#8220;read next&#8221; semantics, and half of the core UNIX utilities are stream parsers that want that semantic. While you&#8217;re in the area though, I&#8217;d ask &#8220;what&#8217;s the purpose of a current directory?&#8221; which is implemented (on Windows) as a process wide value. I&#8217;m guessing it also started (on UNIX) as a programming convenience, but being process-wide has meant that it disappeared as a convenience and reemerged as a headache. (DOS arguably had a different history since it was trying to run non-directory aware applications in the presence of directories.) 

My response:

> If it were just a convenience we could easily bury it in a library. I’m trying to do some hybrid file systems implementation work and it creates complications that seem unnecessary. But who really looks at this old cruft anyway? 
> 
> Current directory is another good one. And the directory enumeration offset a third. 

And his reply:

> As you mentioned though, POSIX was trying to codify existing implementations, and presumably at some point the choice between kernel and library could have been made, but once it&#8217;s been made it&#8217;s hard to change. UNIX libraries always seemed strange to me in that in many cases they&#8217;re super-simple syscall wrappers (similar to NTDLL) but in some cases (file pattern matching) all the heavy lifting is there. Remember that in the beginning shared libraries weren&#8217;t a thing, so requiring functionality to be in a library meant duplicated code while the kernel provided a natural place to share code/functionality. This probably influenced a lot of choices.