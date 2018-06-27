--- 
draft: true
date: 2018-06-27T10:35:26+05:30
title: "Setting Up PowerMockito"
slug: "" 
tags: [tech,android]
categories: [tech]
---

Stubbing and mocking methods are crucial for writing tests for any codebase. For Android, I wrote some unit tests for the modules using [JUnit4]() and [Mockito]() for the mocks. My codebase is in **Kotlin**, so I wanted to write tests in Kotlin as well. [Kluent]() and [Mockito Kotlin](https://github.com/nhaarman/mockito-kotlin) gives us nifty wrappers around Junit and Mockito for Kotlin. 
+ Android Test Runner ?
+ Android Test Rules ?
+ Hamcrest matchers

While writing tests I came across an interesting roadblock. Mockito cannot mock or spy final classes.

What is *mock*ing and *spy*ing

What is solution?

PowerMockito

How did I set it up?

Trouble: Existing tests are failing!

```
Java.lang.IllegalStateException: Could not initialize plugin: interface org.mockito.plugins.MockMaker
	at org.mockito.internal.configuration.plugins.PluginLoader$1.invoke(PluginLoader.java:66)
	at com.sun.proxy.$Proxy12.isTypeMockable(Unknown Source)
	at org.mockito.internal.util.MockUtil.typeMockabilityOf(MockUtil.java:29)
	at org.mockito.internal.util.MockCreationValidator.validateType(MockCreationValidator.java:22)
	at org.mockito.internal.creation.MockSettingsImpl.validatedSettings(MockSettingsImpl.java:186)
	at org.mockito.internal.creation.MockSettingsImpl.confirm(MockSettingsImpl.java:180)
	at org.mockito.internal.MockitoCore.mock(MockitoCore.java:62)
	at org.mockito.Mockito.mock(Mockito.java:1729)
	at org.mockito.Mockito.mock(Mockito.java:1642)
	...
Caused by: java.lang.NoClassDefFoundError: org/mockito/cglib/proxy/MethodInterceptor
	at org.powermock.api.mockito.internal.mockmaker.PowerMockMaker.<init>(PowerMockMaker.java:43)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
	at java.lang.Class.newInstance(Class.java:442)
	at org.mockito.internal.configuration.plugins.PluginLoader.loadImpl(PluginLoader.java:96)
	at org.mockito.internal.configuration.plugins.PluginLoader.loadPlugin(PluginLoader.java:45)
	at org.mockito.internal.configuration.plugins.PluginRegistry.<init>(PluginRegistry.java:18)
	at org.mockito.internal.configuration.plugins.Plugins.<clinit>(Plugins.java:17)
	at org.mockito.internal.configuration.GlobalConfiguration.tryGetPluginAnnotationEngine(GlobalConfiguration.java:55)
	at org.mockito.MockitoAnnotations.initMocks(MockitoAnnotations.java:68)
	at org.mockito.internal.runners.DefaultInternalRunner$1.withBefores(DefaultInternalRunner.java:38)
	at org.junit.runners.BlockJUnit4ClassRunner.methodBlock(BlockJUnit4ClassRunner.java:276)
	... 42 more
Caused by: java.lang.ClassNotFoundException: org.mockito.cglib.proxy.MethodInterceptor
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:335)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 56 more 
```

What's happening? Why us Mockito failing?


**//Explaing how Powermockito works**

Ach! Mockito and PowerMockito is not interacting well. What is the solution? *Mockito2*!

What is the result?

**//Code that mocks using Powermock**