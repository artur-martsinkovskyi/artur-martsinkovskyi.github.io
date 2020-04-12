---
layout: post
title:  "A little bug to ruin everything"
date:   2016-09-10
description: A scary tale how one line of code can cause a plenty bugs and grind your gears as hell.
---

## Introduction

![image](/assets/images/new-debugger-can-discover-security-bugs-in-ruby-code-in-64-seconds-503022-2.png)

Lately, I’ve been in the middle of the development of an interesting project in which soul and flash were mostly derived from Kickstarter and plenty of websites like it. If somebody had said to me back then, that one line of code in the configuration files of my project would make me as angry and tired as I was two weeks after writing that line, I’d laugh loudly. However, it was true - that line was goddamn hard for me to track down and mend.

## Advise for Devise

I was rapidly developing my project, so I assumed it would be a good idea to add some registration-based and user-dependent functionality. Alright, I said. Time to include __:confirmable__ module for Devise gem in my User model and get it working. SMTP-config setup, hiding credentials in Figaro files, a bit of view fixing and then first mail got it way to my testing email address. Everything was great, but… What did I see? Yes, a user got his confirmation link, clicked on it and then he simply was not confirmed. WHAT? After lurking out the Devise guts I patched it to develop forward(basically letting the user use site without confirmation required), but the problem was still here, crawling around to catch me the first time I miss and implement DateTime-connected functionality.

## Second helping for the wolfie

Yaaas, I guess you get what comes next - HERE COME THE DRUMS, other words, additional features that need time and dates to be a part of them. Nothing, literally nothing that had to be working with dates, time and timezones functioned for real. You pass a date or time, even basic __:created_at__ to the DB via ORM and when you get it back it returns just __nil__. Nothing you try mends the problem. Resetting the DB? Naaah. Debugging saving process? Nope. The funniest thing was, in fact, the detail that every freaking date inserted in the DB appeared in it, according to the PostgreAdmin and other database administration program information, but each time I tried to get it Rails returned me the oddity of nil. As you may presume, it ground my gears as if I was watching Uve Boll films for an entire week

## Taming the beast of time zone


After all the failed tries and experiments on my project, I was tired, angry and full of hate. Nils were everywhere in my mind and for the last shot, I googled just literally the case I came across. In the middle of the first search page, I saw this [stackoverflow link](http://stackoverflow.com/questions/20255272/rails-4-model-returns-always-nil) and, oh… Flying Spaghetti Monster, it worked. For some reason, when you pass to the config of timezone the timezone in the wrong format, the application doesn’t indicate any error or exception. It doesn’t even point you are a dummy, no. It just gets it’s ORM time and date operations broken, because without proper timezone given it does not properly interpret the dates in the DB and ends up taking back nils instead of any date at all. This is awfully bad and hardly trackable behavior for the advanced data mapping system as Active Record.

## Conclusion

Summarizing the stuff written above I can tell you that every, even the smallest change in your code shall be thought over
and mentioned in your documentation and changelists. Remember, even the smallest bug can get to stumble your entire solution
working process the time you least expect it.
