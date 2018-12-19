---
layout: post
title: "Aspects of TDD"
date: 2009-03-30
comments: true
disqus_identifier: 45
categories: [DDD]
redirect_from: "/Post/45/Aspects-of-TDD.aspx/"
---
This post was supposed to be about the layering in my sample project,
but I thought I would talk a bit about TDD as its new to me. This is by
no means an exhaustive post on TDD

*Test Driven Development*

TDD could be told as an approach in development, where we are writing
tests to know the area for which we are building the software. Tests
written during the development phase helps you in understanding the
domain, and, after development tests helps us document that system.
Following are a few things about TDD that I have understood

*The Steps*

1. We write a failing test. We have to identity whether the test is testing what is intended in this step.
2. We make sure the test written in step 1 passes.
3. We refactor the code written in step 2.
​
We can also segregate a single test into 3 sections viz. *Arrange*–
Where we setup everything that is there to test, *Act*– We do everything
there is to do with the test, *Assert*– We check our assertions to be
true or not.

*But its hard to test*

There are certain areas that are hard to test, like UI ( Its all the
more reason to split the UI aspects from the domain model ). Then there
is the database which makes it even harder to test. Why? because working
with actual db is cumbersome, we have to maintain the state of database
for every run of the test. This could be done for integration test, but
while in development stages, stubs or mocks could be used, and lastly
there is the code of the tests themselves which we’ll have to maintain,
but that should be ok, because the tests helps a lot in doing
maintenance work.

*Stubs and Mocks*

There will certain areas in our development which when tested might be
too slow or difficult to setup for example the UI or the database
operation. To overcome these difficulties we can use stubs or mocks. 
 
A stub is a light weight implementation of a real object. It is a way to
setup the expected values that can be used within the test. In tests
stubs we create a class that impersonates the actual class that we have
to test. For example if we are to test a class that does database
operations, we create a stub class that is free of the database and use
this stub class in our tests. The thing to note here is that we are
testing the database operation methods and not the actual database call
and also we provide the input and the output via the stubs. A mock is
also an implementation of a real object, but unlike stubs we can set our
expectations in mocks on how it should be used and it also provides self
validations on the expected results. Mocks are usually created
frameworks.

You can read about the differences between mocks and stubs
[here](http://martinfowler.com/articles/mocksArentStubs.html)

I will be writing more on TDD as I dwell more into the development of
the sample application.

