---
layout: post
title: Myopic rationality of software kludge
date: 2019-05-31
description: Crust is invevitable, why do we let it grow? Maybe there is a bit of bad rationalization here, huh?
---

**TL;DR** *People love with their eyes, not their minds. They usually don't estimate what they don't see or comprehend, so without of proper voice of reason, software project turns into cruft pretty quickly. It seems rational to do so in the short-term, but strategically it is a way to failure.*

## Clinging to a cliff

People are not good at predicting the future. Gosh, even thinking about the future realistically varies from pretty hard to impossible. Future is a slice of the entangled mess of reasons and consequences that create a huge and highly intertwined system, so no wonder our little biological computers are bad at a simulation of it. We may dream about the future, we may create an approximate vision of what is going to happen, but we never know exactly. Looking further in the future it becomes harder and harder to see even the unfocused blurry vision of what is going to happen. So people do the best they can - they rely on the old mechanisms nature has built into us a long time ago - intuition, aspiration, Gestalt of the future if you wish. This vision is highly material, it relies on what they see and feel rather than what they reason about. They see the future as a continuation of the present, so their mind cuts the corners on unknown or uncertain things focusing on what it deems important - actual factual data it manages to retrieve from the surrounding world. But there are a lot of factors we cannot properly perceive but still need to take into account to judge rationally.

## Step in line and stand in line

It takes a deliberate and conscious effort to start taking such unknowns seriously. Software development team managers, business owners, business analysts - all of those people are doing future predictions as their job, so they've got to excel in it. Unfortunately, this is not always the case. They try, they really do, but it is quite hard to do it properly unless you know about the traps of your mind and listen to those doing the job whose actual state of affairs is pretty hard to estimate without their part. Failures of design and wrong functionality take immediate effect and signal that something is wrong. Managers are used to handling those warnings and know how to track them when it is not yet too late. But they don't do it with crust simply because only measurable metrics - expansion of feature development time and bugs seem fairly tangential to the perceived code quality and the only actual signal you may get is frustration of developers and their moans while they dig through the codebase trying to fetch things together in a manner that will not break everything. Those are frequently ignored due to their immeasurable nature. Crust works like radiation - it kills off the project without you seeing the effect until the fatal or semi-fatal damage is done. Basically, when you start noticing the crust it may be too late to fix it while doing the new features because the damage of cut corners, wrong abstractions, and improper domain language will stack up faster than development team resolves it.

## We wanted the best - you know the rest

Once I heard a nice story from a fellow developer. He said that his team lead liked to repeat one interesting observation.  He used to say that there are two stages in software development:

- Do it dirty and make it work
- Clean up and make it shine

"Nobody ever reached stage two." - he usually finished the saying. And I agree with this. Mostly, people don't seem to integrate dirt and future problems in their minds with something that looks beautiful on the outside. This common misconception along with "know it all" attitude and a will to fulfill customers' needs to the fullest extent creates an effect of the local maximum. Once a manager manages to get a solution in a much faster manner using hacky and patchy solutions he starts falling into a spiral, a spiral of "we will fix it later". This strategy does not take an immediate negative effect, so it tickles their minds in pursuit of more and more immediate benefits. Even though they may think and believe that technical debt is a thing and that it requires handling, their human nature and instincts say that crust is not that big. They cannot check it up themselves, so usually it is too late when the big break comes and application is suddenly far away from normal. We can see that it gets even bigger if we notice the practice of one-two year rotation managers and software engineers are used to in modernity, so the crust they are creating maybe not even their own problem to solve and lie on the shoulders of poor fellows getting on the job after they get away. In order to avoid it, we as an industry have to integrate refactoring and continuous synchronization with the domain as a part of everyday practices for every software project. Some do it, but most of the projects I've seen or heard about move from one catastrophe to another.


## Although, also

Sometimes the crust is here to stay. You need the project to be done and you need it fast. Maybe you need to be faster than your competitor, there is a pool of venture capitalist sharks behind your door or simply your idea is not yet formulated good enough so moving fast and breaking things is the only option to survive and strive. In that case the only things you can do is to choose less crust-prone technologies and languages, those that can do prototyping fairly easily(Ruby gets out of the corner) and then skip to other tech stacks once you are done, hire a nice bunch of seniors that have seen a hell ton of kludge in their life and know how to make it less poisonous. But you should remember that debts are here to stay and they have got to be paid sooner or later. The price may be a success of your project if you take it to the extreme.

The only question is, is the crust worth it?

## Aspirations

[Is High Quality Software Worth the Cost? by Martin Fowler](https://martinfowler.com/articles/is-quality-worth-cost.html)
