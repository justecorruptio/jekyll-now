---
layout: post
title: "Fun Bugs I've Fixed #2: Geopolitics"
tags: Bugs
---

Let's start with a short history lesson.  Some of you young'uns may not remember this, but there used to be a country named Yugoslavia. It was formed as a kingdom in 1918 after the Great War as a result of the break-up of the Austro-Hungarian Empire. At the end of the next world war, its monarchy was sacked and replaced with Communism.

Everything was fine and dandy (not really), until 1991 when the different peoples living in Yugoslavia decided they didn't like each other enough to share a country anymore. So they started the Yugoslav Wars, and broke apart into seven different countries: Bosnia and Herzegovina, Croatia, Kosovo, Macedonia, Montenegro, Serbia, and Slovenia. And here we are today.

So on to the bug... I was working on a machine learning system to filter spam as a service. One of the most important functions of my job was "feature engineering," or the process of finding attributes of emails that look spammy. For example, if a message misspelled a pharmaceutical, or contained links shady blogging platforms, or had red text on black background, that message is more likely to be spam.

One particular feature that we had was "If the message contains a link, where is that domain name hosted?" Data centers in South-East Asia, Russia, Brazil, etc. tended to host spammier sites. So you can see where this is going...

One day -- April 1st, 2010 to be precise -- our logs went crazy. The anti-spam engine's efficacy started to degrade. We had kept a static list of valid country codes. Part of that list was the now-defunct Yugoslavian `.yu` top-level-domain (TLD). Spammers still controlled many `.yu` domains and linked to them in their spam. According to our list, a Yugoslavian domain was valid, but then trying to resolve it failed.

Apparently, it took nearly a decade for the ICANN to shut down `.yu` TLD. But on March 30th, 2010, they deleted all Yugoslavian domains, and two days later, removed `.yu` from the DNS root zone. Finding and fixing that bug was actually pretty straight-forward, but it was a good reminder that defensive coding means accounting for even geopolitics.