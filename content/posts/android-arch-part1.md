---
title: "Android Architecture Sandbox #1"
date: 2018-06-25T11:14:44+05:30
draft: false
tags: [tech,android]
categories: [tech]
---

Around May of last year Android introduced a bunch of new classes around organising code better. Till then Android team never really took a stand to tell the community how to organise their codebases. What I observed was activities started developing unwieldy View classes (Activities and Fragments) that did everything from spawning new threads, making network calls, performing disk writes to anything and everything you could require the application to do.

An approach like that would introduce a lot of obvious problems
<ul>
<li>Maintainability</li>
<li>Readability</li>
<li>Reusability</li>
<li>Testability</li>
</ul>

Android problems
<ul>
<li>Memory leaks</li>
<li>Lifecycle management</li>
</ul>

This is something that was driven into my head by <a href="https://www.infoq.com/presentations/Simple-Made-Easy">Rich Hickey</a>
<span><h2 style="{margin: auto}">Simple doesn't mean easy</h2></span>

The solution for the problem would be to break down these monolithic classes into seperate classes, i.e. <strong>Seperation of concerns</strong>.
This would enable the logic related to rendering of the screen itself be consolidated in one place whereas the business logic would be moved to another class. The assumption here is that we have done a good job to abstract the logic that is not relevant to either of the classes (API clients, Queries,...), the next question is how these two classes interact. As you may have already, this not an exclusive problem for Android but for most software user interface systems.

So the available solutions for us are: <br>
 <strong>Model-View-Controller</strong>, <strong>Model-View-Presenter</strong>, <strong>Model-View-ViewModel</strong><br>

I was introduced to <a href="https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html">The Clean Architecture</a> when we started working on building a new application from scratch and we had to decide which solution suited us the best. The primary cornerstone being <strong>Dependency Rule</strong>: The dependencies only point inward i.e. the dependencies are unidirectional. This helps the codebase retain independency between the layers of seperation.

The introduction of <a href="https://developer.android.com/topic/libraries/data-binding/">Databinding Library</a> enabled us to implement an MVVM. But as the codebase aged it was soon realised the implementation was not ideal with the highlighted problem being a lack of proper life-cycle awareness and leaky abstractions.

This makes me want to revisit the MVVM pattern in Android using these libraries and on the way explore the latest methods to writing <a href="https://github.com/00-00-00/android-arch-sandbox">a simple android application</a> using <a href="https://developer.android.com/topic/libraries/architecture/viewmodel">ViewModel</a>, <a href="https://developer.android.com/topic/libraries/architecture/room">Room</a>, <a href="https://github.com/ReactiveX/RxJava">RxJava2</a>