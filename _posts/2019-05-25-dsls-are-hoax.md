---
layout: post
title: DSLs for non-programmers are a hoax
description: DSLs are suggested to be an aid for the users that want to extend the programs they work with easily, but do they work?
date: 2019-05-25
---


## The bright future

In software engineering there were always ideas of automation and generality that will lead the body of work for the field to be elevated to the context where someone who does not know ins and outs of particular domains low structure and basis to perform tasks and fulfill the needed features easily. The belief in an all-powerful magic wand that is able to make most of the professionals of the field obsolete. Visual programming, code generation - there are a lot of flavors that muddle the minds of every new generation of programmers and executives in the field. Every time it fails for a reason - the domain is complex and so is the software engineering. Any simplified and constrained procedure that magically lets you create software without actually writing it sooner or later reveals itself as inefficient and inadequate when complexity of the features rises and details emerge form the murky minds of the business team.

## Kings in their domain

Another idea that seems treacherously different from beforesaid ones is the domain specific languages concept.  As I see it, domain specific language is an extension of general-purpose language or a brand-new separate one that exists in the context of particular domain, trying to simulate the structure, concepts and vocabulary used in the domain in order to let people write code in a highly abstracted fashion leaving off technical details and focusing on important topics and actual features. Sounds great, yeah? Programmers extend the DSL as needed, business team writes its own code and configures the program the way the would have written requirements for the development team, everything is developed faster and more in line with what was actually intended, everyone is happy, world is getting better.

## [A-Z]*L in the wild

As with any silver bullet, the theory looks amazing, but in practice it turns out to be quite *shite*. Why so? Remember SQL? [Actually](https://dl.acm.org/citation.cfm?doid=800296.811515) it was intended for non-programmers such as engineers, accountants or architects(not software ones).
>However, there is also a large class of users who, while they are not computer specialists, would be willing to learn to interact with a computer in a reasonably high-level, non-procedural query language. Examples of such users are accountants, engineers, architects, and urban planners. It is for this class of users that SEQUEL is intended...

The last time I had a business analyst that wanted to write SQL, he tripped over one join and asked me to do the job. SQL is used widely, but it does not work that well for people who are not programmers. HTML and XML were also originally intended for non-programmers - where are they now? They are used by amateurs or enthusiasts sometimes, but most of the time programmers do the job.

## Domain specific madness

Those were the existing languages developed by committees, thought and worked through, more or less general. They did not become widely popular among business people and other professions because they needed some attention, learning and understanding from the user in order to be effective. In the end when the serious stuff comes in it was always a better choice to hire someone who really understood what he was doing. In some sense they succeeded. But, every once in a while an executive, CTO or somebody else on the semi-technical/semi-management position gets a brilliant idea of writing own DSL for business processes of the project. Let's do it, people, write a language and it will be easier for everybody to work on the project. They choose Ruby, Python, some flavor of LISP or JVM family and start writing code that must look similar to proper English of the domain. Business people start using it, handing a lot of complaints on mismatches, dumb stuff and insufficient documentation. Programmers fix and extend the DSL, but abstraction starts to leak revealing corner cases and essential technical details. More and more work and actual domain code writing is done by the programmers and the project ends up with another wheel developed inside the team that is not really used by the business, but programmers have to write code for it because the codebase is quite large enough already and rewrite will take ages.

Why does it happen like that? There are multiple reasons. Business is complex and domains transform every now and then. Domain specific language is intended to represent the body of the domain and in order to sync up it needs to change frequently same as its real world counterpart. It overlays the need to write fluent interface that will be convenient to use which makes the task even harder. Actual language is written by the people that don't know that much about the domain - software engineers. There will be missed significant details, improper wording and all kinds of issues with understanding. The user of this language, a business person, is not a software engineer, so when the existing language won't be enough for some feature it will inevitably lead to another language extension and more changes to codebase that may cause errors of its own. Domain languages are constrained, ideas are not. Unfortunately, degeneration and rot is immanent, so sooner or later support cost and meandering of the monster created by a thousand extensions starts to outweigh seeming benefits of the DSL.

## Not all DSLs

After all of this, you might have though that I deem DSLs to be a totally bad idea. I don't. I dislike them only when they are developed to display some business domain or to be handed over to regular users, non-programmers. If it is developed for programmers or for the domain that does not change frequently it is perfectly fine. There are AWK, RSpec, EJS, Emacs Lisp, bash scripts and a lot of others that are cool because their domains are steady and they are used by professionals.

P.S. I hate Gherkin, Cucumber and other DSLs that let you write the tests that "read like English", but that is the conent for another story.

**EDIT:** After having read the comments on Hackernews and Reddit, I've changed my mind a bit. Yeah, SQL, TeX, Excel scripting and things alike them(e.g. general-purpose tooling that does not focus on any particular domain and works solving a general problem) mostly work properly and serve as a nice bridge between professionals and their tools allowing people to enhance their work with a bit of coding. They do apply as successful DSLs. The DSL fashion I oppose in that article is the faishon of self-baked business-specific tools that are intented to be used by users that are not programmers or even tech people. Those require much more effort than a regular system and are seldom to be done well. So it is better to think twice before creating your own DSL unless you're solving a problem that will last relatively permanent in formulation for ages.

