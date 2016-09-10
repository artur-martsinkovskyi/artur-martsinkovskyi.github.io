---
layout: post
title:  "A little bug to ruin everything"
date:   2016-01-10
excerpt: "A scary tale how one line of code can cause a plenty bugs and grind your gears as hell."
tag:
- Rails
- Ruby
- Debug
comments: true
---

## Introduction

Lately I've been in the middle of development of an interesting project which
soul and flash was mostly derived from Kickstarter and a plenty of websites alike
it. If somebody had said me back than, that one line of code in the configuration files
of my project would make me as angry and tired as I was two weeks after writing that
line, I'd laugh loud. However, it was true - that line was goddamn hard for me to track down and
mend.

## Advise for Devise

I was rapidly developing my project, so a assumed to be a good idea to add some 
registration-based and user-dependent functionality. Alright, I said. Time to include
__:confirmable__ module for Devise gem in my User model and get it working. SMTP-config
setup, hiding credentials in Figaro files, a bit of view fixing and then first mail 
got it way to my testing email address. Everything was great, but... What did I see?
Yes, user got his confirmation link, clicked on it and then he simply was not confirmed.
WAT? After lurking out the Devise guts I patched it to develop forward(basically letting user
to use site without confirmation required), but the problem was still here, crawling around to catch me first time I miss and implement datetime-connected
functionality.

## Second helping for the wolfie

Yaaas, I guess you get what comes next - HERE COME THE DRUMS, other word, additional 
features that need time and dates to be a part of them. Nothing, literally nothing what
had to be working with dates, time and timezones functioned for real. You pass a date or time,
even basic __:created_at__ to the DB via ORM and when you get it back it returns just __nil__.
Nothing you try mends the problem. Resetting the DB? Naaah. Debugging saving process? Nope.
The funniest thing was in fact the detail that every freaking date inserted in the DB appeared in
in it, according to the PostgreAdmin and other database administration programs information,
but each time I tried to get it Rails returned me the oddity of __nil__. As you may presume, it
it ground my gears as if I was watching Uve Boll films for an entire week.

## Taming the beast

After all the failed tries and experiments on my project I was tired, angry and full of hate.
Nils were everywhere in my mind and for the last shot I googled just literally the case I came across.
In the middle of the first search page I saw this [stackoverflow link](http://stackoverflow.com/questions/20255272/rails-4-model-returns-always-nil)
and, oh... Flying Spaghetti Monster, it worked. For some reason, when you pass to the config of timezone 
the timezone in the wrong format application doesn't indicates any error, exception, it doesn't
point you are a dummy, no. It just get it's ORM time and date operations broken, because without proper timezone
given it does not properly interpret the dates in the DB and ends up taking back nils instead of any date.

## Conclusion

Summing up the stuff written above I can tell you that every, even the smallest change in your code shall be thought over
and mentioned in your documentation and changelists. Remember, even the smallest bug can get to stumble your entire solution
working process the time you least expect it.
