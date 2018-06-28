---
draft: false
date: 2018-06-26T21:22:02+05:30
title: "Android Architecture Sandbox #2"
slug: "" 
tags: [tech,android]
categories: [tech]
---

The objective is to write an android application that will fetch some data over the network, store it locally and display the data in a list-detail format.

Since we want to try avoiding couping. We will try adopting the Observer Patter i.e. we'll have modules that emit events and other modules react to to the event. We can use [RxJava](https://github.com/ReactiveX/RxJava/wiki) for a publish-subscribe mechanism. Since we are using RxJava on Android, [RxAndroid](https://github.com/ReactiveX/RxAndroid) which provides us nifty `Scheduler`s for managing the threads.


Breaking down the requirement, we can see the modules we need for the application.


>fetch some data over the network


So FYI, the API we are going to fetch data from is going to give us data in a JSON format. What we are going to need is a network module. [Retrofit](https://square.github.io/retrofit/) and [Gson](https://github.com/google/gson) is going to keep our head sane while building this module. We need to use gson converter for Retrofit

 `implementation "com.squareup.retrofit2:converter-gson:$retrofit_version"`

 Since we decided to use RxJava, we need to use a call adapter for Retrofit.

`implementation "com.squareup.retrofit2:adapter-rxjava2:$retrofit_version"`

The API also has a timestamp as a field. Since we are not blessed with the Java 8 Time APIs on Android, we need can use the next best alternative a backport of the JSR-310: [Jake Wharton's ThreeTenABP](https://github.com/JakeWharton/ThreeTenABP)

`implementation "com.jakewharton.threetenabp:threetenabp:1.1.0"`


>store it locally


Android architecture components gives us [Room](https://developer.android.com/topic/libraries/architecture/room) which is an ORM over SQLite. The tedious nature of managing DB Connections and Queries using SQLite is something we can live without. When I had previously used [Realm](https://realm.io/) for mobile database, it had shown me the freedom. It let me move so fast that my estimates were short by days. We want to try how easy Room is to work with and how well it does it's job on the surface. I may need to write tests to effectively test the perfomance though. Let's add it to backlog for now. 


>display data in a list-detail format


We need to think about our *views* now. We have `ViewModel` from Android architecture components. That leaves us with our Activities and Fragments and what we need inside them. We will use `DataBinding` library for binding fields and adding some limited logic to the layout files themselves. I've found in my experience that they keep the view classes really neat by avoiding the need to fetch each view item by id and setting value after inflation. Although it's a slippery slope when you start writing intricate logic into the layout files, debugging becomes a bit hectic since the view logic might be spread across the view class and the layout file. But the trade-off is very well worth it.


We are going to use [ButterKnife]() for managing `View`s using annotations, which helps avoid some boilerplate code.

The usual suspects: `RecyclerAdapter`, `CardView` and other [Design Library](https://developer.android.com/topic/libraries/support-library/features#material-design) classes.

We also want to try using `ConstraintLayout`, [Paging (Beta)](https://developer.android.com/topic/libraries/support-library/features#material-design)

[Next part](/posts/android-arch-part3), we'll set up the API call using Retrofit, Database using Room and ViewModel all of whom will be using models in LiveData






