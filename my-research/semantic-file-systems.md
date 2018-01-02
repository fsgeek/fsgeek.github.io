---
id: 25
title: Semantic File Systems
date: 2016-11-27T13:29:13+00:00
author: fsgeek
layout: page
guid: https://fsgeek.ca/?page_id=25
---
My primary research objective and interest is in the area of semantic file systems.  I have been working in the area of file systems for many years now and have considerable hands-on experience in designing, developing, analyzing and evolving such systems.

I was first introduced to the concept of semantic file systems around 1990 when David Gifford gave a talk to my team at Transarc Corporation, where we had been working on AFS, a commercialized version of the Andrew File System.  I thought his presentation was interesting and the concept of enhancing the ability to locate information within a file system an interesting one.

Since that time, I have bumped into a common implementation model for such file systems – typically it involves modifying a database in such a way that it supports semantic information.   While some versions of this are highly successful, e.g., Google’s File System, attempts to do so for local file systems tend to suffer from a variety of problems, in particular performance.  Thus, I have learned over the years to be deeply skeptical of such systems – the _idea_ is interesting but the _realization_ is flawed.

Ironically, it was recently, while taking a class in Human Computer Interface that it became clear to me why we need semantic file systems.  Our current file/directory metaphor does not actually serve users particularly well because it offloads cognitive processing onto the users.  From even casual conversations with users, there was fairly common agreement that finding content on the local computer system is a struggle.  Common solutions are sub-standard: download the content again or do brute-force search for the content.  This is hardly a new observation – indeed, “Hierarchical File Systems are Dead”.  Despite recognizing this fact, we continue to use hierarchical file systems.

In researching this area of interest, I can see two specific reasons for this: (1) hierarchical file systems are inherent in the OS interfaces themselves, both the system level APIs as well as the installable file systems interface (e.g., VFS); and (2) semantic file systems are normally implemented as databased of attribute information on top of the existing file system, although hFAD was certainly an exception to this general rule.

Thus my first observation: to be sufficiently useful, a semantic file system needs to be written as a kernel level component.  It must support the needs for the semantic tagging system.  It must be sufficiently flexible to permit different tagging schemes.  It must provide modern file system guarantees.  It must support the file/folder metaphor, but must be willing to relegate it to the status of a “legacy” interface.

My second observation is that the tagging systems generally relate to _content_ of the files.  However, these do not address the need for tagging that better aligns with human cognition.  For example, humans tend to create _temporal relationships_ between activities and hence content.  Thus, a user might wish to find something they were working on “around the same time” as something _else_ that is temporally related.

My third observation is that a viable solution must address two very different “users” of the semantic file system.  It must address applications that identify and find content, such as a program that must load its executable image, associated DLLs, configurations files, etc.  It must also provide strong support for human users attempting to find content they have previously accessed.  The way in which people search for things will vary substantially, unlike the way in which programs search.

Thus, I envision a system in which the core file system provides strong, flexible support for semantic operations, including temporal locality information, as well as modern file system requirements for reliability and flexibility.  In addition, it must have a dynamic mechanism for constructing semantic tags that combines an understanding of file content as well as the manner in which the system users search for that content.

This strongly suggests a cross-disciplinary approach to the problem, incorporating modern natural language processing and machine learning techniques for adaptive semantic tagging that work cooperatively with the native file system to work on a model of dynamic improvement for storing those attributes.

My strength in undertaking this research is that have a demonstrated track record in building robust, production quality file systems.  I understand the internals of multiple operating systems and am comfortable exploring outside the fixed file/directory/VFS paradigm.   I am actually more comfortable doing kernel mode development than user mode development, which is a bit different than most of the people who have done research in this area in the past.

I understand the complexities of designing local disk file systems and am comfortable researching ways in which attribute information can be stored flexibly.  For example, Formal Concept Analysis look promising as a mechanism for designing efficient storage of tagged attributes and embedding native support for this in the file system will permit robust performance.  Further work needs to be done in evaluating effective mechanisms for including temporal locality information as well as how to incorporate a legacy file/folder paradigm into this new file system to permit support for legacy interfaces.

Another strength that will serve me well in pursuing this research is that I am willing to look outside the normal solution space.  Solutions are often found by looking at the problem from different perspectives. For example, I have implemented many transactional systems, including the transactional file system mechanism in Episode, in which I have found interesting ways that weak transactional systems provide a good balance between performance and correctness.  For example, in Episode we handled atomicity, not by blocking forward progress, but rather by merging disjoint transactions together so that the system could proceed.

My recent studies in the Georgia Tech MSCS program have given me insight _outside_ my area of expertise.  This has been helpful in looking beyond what I have done before and identify areas in which my skills can be augmented and permit me to explore potential new directions that are atypical of what someone doing research in this area would be willing or able to explore.