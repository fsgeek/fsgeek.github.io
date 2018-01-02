---
id: 164
title: For those problems that are nails, I have a sledgehammer
date: 2017-10-24T16:19:11+00:00
author: fsgeek
layout: post
guid: https://fsgeek.ca/?p=164
permalink: /2017/10/24/for-those-problems-that-are-nails-i-have-a-sledgehammer/
categories:
  - Research Ideas
---
I&#8217;ve been involved in a project that requires I learn more about blockchain than I ever really wanted to know.

I had a basic appreciation of it from reading the Nakamoto paper many years ago.  It provides an insightful solution to a challenging problem &#8211; the problem of distributed consensus in an untrusted network.  There is an agreed mechanism (the protocol) and an incentive to participate (the fee).

Why is an untrusted network the problem?  Because this came from the world of digital cash, and the concern in that environment was that someone could &#8220;double spend&#8221; money.  The blockchain model makes that effectively impossible.  It does so in an interesting way &#8211; by combining a consensus protocol (getting multiple systems to agree on a given outcome) along with an incentive to behave (participants get _paid_ to behave).  It&#8217;s an intriguing way of reaching common consensus.

But it has limitations.  Like _any_ consensus protocol, it takes time to send messages back and forth and reach an agreement.  Further, blockchain requires that nodes attempt to solve a cryptographically hard problem.  The node that is successful gets the reward.  The algorithm is designed so that it takes about 10 minutes for someone to reach a valid solution.  These solutions are easy to verify, so once someone has figured out a solution &#8211; which indicates real work &#8211; they present their answer and it is echoed throughout the network.   By chaining the answer for the current block into the prior block it essentially creates chain that is not realistically mutable (e.g., it cannot easily change).

There are more technical details (Merkle Trees and cryptographic hashes and so forth) but the final solution is a &#8220;public ledger&#8221; that shows who owns what in the world.

Here&#8217;s the rub: this is interesting technology, but it is not the solution to every problem.  As is so often the case in computing, though, we pick &#8220;the shiny new tool&#8221; and insist it _must_ be the right tool to chose for our problem at hand.  The challenge then for those of us who have been through this before is to actually analyze a problem and figure out a good solution.  Of course, sometimes we&#8217;re called upon to utilize a particular tool, like blockchain, regardless of whether or not it is the right tool for the job.  When that happens there&#8217;s an unenviable task of trying to explain why it is not the right solution.  Doing so is tough, as it is often **not** what a customer wants to hear.