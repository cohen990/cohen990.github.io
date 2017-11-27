---
layout: post
title: ".NET Core - Ready For Action?"
date: 2017-09-20 16:25:06
description: .NET Core is new in town - but can you work with it?
tags: 
- .NET
- .NET Core
- microsoft
---
Short answer - Nope! :D

The Good
--------

.NET core 2.0 was recently released. In general, I love working in .NET core. I think it has a lot of potential speaking from a dev experience standpoint. I'm a huge fan of working from the command line, and .NET core allows me to do that. In fact, it offers a lot of flexibility with how I work. No longer am I bound to the weighty and opinionated Visual Studio. I can user Rider if I want to. Hell, I can even use vim and the .NET core CLI to work, although I wouldn't bet on that being too sustainable when working on large projects.

I can work very easily from Linux or from OSX too. It at least fulfils it's promise of being cross platform (although I have heard numerous complaints from other people that it's not all roses in the linux department).

Essentially, it provides me with far more flexibility than the old .NET world ever did.

The Bad
-------

The versioning... I'm not the first person to say this and I'm sure not gonna be the last. What with the .NET core and the netstandard versions not always lining up, having to refer to a table to figure out which version of what will be compatible with .NET 4.5.2 and then the SDK, the runtime and the CLI all have different version numbers. It's a total mess. It is navigable, but it is a total mess.

Other problems include the lacklustre open source community. For example, the only alternative to nCrunch that I've ever heard of is ContinuousTests - and frankly it didn't work very well when I tried to use it. . None of this is to disparage the wonderful and dedicated people writing open source software for the .net ecosystem - but certainly something is missing. Having said that, there is a test watcher that you can use from .NET CLI... but it doesn't do any of the smart compilation or partial test running that nCrunch does.

The Ugly
--------

The tooling support if you decide to work in a non-windows environment is not enough, unfortunately. If you want to work following TDD or even caring a little bit about tests, then you're out of luck, really.

TDD can be done, but there's no profiling API available yet... this means there's no code coverage available.

If you're working in an enterprise environment, how do you think your stability team is going to react when you tell them you have no code coverage? If you working in legacy, how can you refactor without seeing the code coverage? Certainly you can manually inspect the coverage but that's practically inviting new bugs and regressions.

Recommendation?
---------------

I recommend downloading .NET core and playing around with it. It's a fun tool, but not a business-ready tool yet.
