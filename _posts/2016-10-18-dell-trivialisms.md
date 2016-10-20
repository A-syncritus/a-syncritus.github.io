---
layout: post
title: Dell 1F66 and 6FF1
subtitle: Wash and repeat to secure
# bigimg: /images/path.jpg
---

More than two years ago Dogbert reported that [bypassing the Dell 1F66](http://dogber1.blogspot.com/2014/07/dell-1f66.html) password validation was a trivial excercise in patience.

Curious I looked deeper and turns out that whatever BIOS engineers were tasked with changing the password hashing algorithm, they did not spend too much time coming up with novel ideas.
Just like with the [Dell 1D3B]({% post_url 2016-06-09-dell-1d3b %}) they just added more code that does nothing in making hashing more secure. Specifically:

* They changed the character map yet again.
* Changed the number of MD5 iterations
* Duplicated the MD5 loop and switched around the four stages

Interestingly enough the engineers probably realized that simply chainging the code around does not add anything to the security of their algorithms, so when they were tasked with the Dell 6FF1
bios they did not bother to touch the code, but simply change the character map and some constants.

Given that it's been two years since the original publication, and Dell had a plenty of notice I had published the code to the [free BIOS recovery](https://bios-pw.org/) site and my [GitHub fork](https://github.com/A-syncritus/pwgen-for-bios),
May be they will make the effort and fix the code. Then maybe they will not, but here is [a good read](https://dogber1.blogspot.nl/2010/07/how-to-protect-better-secure-bios.html).

A friendly public notice - don't pay for your BIOS recovery, call your vendor or do it for free.