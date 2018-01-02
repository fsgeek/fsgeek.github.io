---
id: 147
title: 'Windows and &#8220;reserved names&#8221;'
date: 2017-08-08T20:03:00+00:00
author: fsgeek
layout: post
guid: https://fsgeek.ca/?p=147
permalink: /2017/08/08/windows-and-reserved-names/
categories:
  - Windows
---
I&#8217;ve fallen into the habit of answering some questions on [Quora](https://www.quora.com) and those which involve answering questions about Windows file systems seem to garner the most interest and the most disagreement.

Yesterday I [answered a question](https://www.quora.com/Why-cant-I-save-a-folder-named-con-in-Windows/answer/Tony-Mason-10) entitled **Why can&#8217;t I save a folder name &#8220;con&#8221; in Windows?**  I decided to respond to this because the other answers that I read said that Windows reserved this name.   My response demonstrated how you could easily do this with bash running in WSL.  Some comments seemed to think that using bash was cheating, so I sat down and wrote a simple program to create folders with reserved names.  It isn&#8217;t bound to WSL and executes as a standard console program (so it is executed from the command line).  I&#8217;ve [published the source code on github](https://github.com/fsgeek/condemo).

It&#8217;s been a few months since I&#8217;ve posted anything about Windows file systems and I realized that I should start doing so again.  So, that little application program inspired me.

The difficulty that I have with assertions that &#8220;Windows doesn&#8217;t do that&#8221; or &#8220;Windows doesn&#8217;t allow that&#8221; is that they often conflate the limitations of applications, libraries, or subsystems as being limitations of _Windows_.  Windows NT was developed to be a general purpose host operating system.  The expectation was that it would have subsystems which implemented specific execution environments. While this includes Win32 (the subsystem most often used by applications) it has never been the only subsystem available.  Windows NT 3.1 shipped with Win32, NTVDM, OS/2, POSIX, and native subsystems (though native is more &#8220;not bound to a subsystem&#8221;).  I had also heard rumors of a UNIX System V Release 4 subsystem as well, but to the best of my knowledge it was never released and I never saw it.

Window dropped support for OS/2 in Windows XP.  The POSIX implementation changed as it became more like a library that called Win32 and native as needed.  What remained of POSIX was dropped in Windows 8.1.  The [Windows Subsystem for Linux (WSL)](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) has been added in Windows 10. It is not based upon the old POSIX subsystem, but it certainly exploits functionality that Windows includes _because_ of the POSIX subsystem&#8217;s requirement.

Win32 does not have a concept of **[fork](https://en.wikipedia.org/wiki/Fork_(system_call))**, but Windows includes all the support necessary to create a duplicate address space, either via **NtCreateProcess** or **NtCreateUserProcess** (you can see an example of the latter, including protected processes [here](https://github.com/Microwave89/createuserprocess)).  This is used to implement this functionality.

NTFS has always supported the full range of POSIX compliant names.  The downside to using this is that it means Win32 application (like Explorer) can have problems accessing them.  That&#8217;s not a limitation of _Windows_ it is a limitation of the _application_.

I&#8217;ve heard several people tell me that &#8220;Windows&#8221; must include everything installed, not just the operating system.  I disagree with this characterization, but I&#8217;ll address the why of that in a future post.

Bottom line: Windows itself does not have restricted names based upon MS-DOS.  Win32 applications usually do, however.  I would also note that Explorer cannot handle long path names &#8211; somewhere within the code is a hard coded buffer that is smaller than the OS level maximum path length (which happens to be **32767 **16-bit UNICODE characters).  NTFS limits each name component (directory or file) to 256 bytes, but ReFS limits the component name size to 32767 (and this has inspired me in another area I&#8217;ll talk about at some point as well).  Other file systems may have their own limits, too.

That&#8217;s enough for now.  I&#8217;ll find more interesting things to discuss when it comes to Windows file systems, though.