---
layout: post
title: "Aspects Of DDD. Part II"
date: 2009-03-25
comments: true
disqus_identifier: 44
categories: [DDD]
---
This is a continuation post. You can read the first part
[here](http://www.simplyvinay.com/Post/43/Aspects-Of-DDD.-Part-I.aspx).

*Aggregates and Aggregate Root*

In a software there can invariably be many objects, and some of these
objects are bound to be related to each other in some way. These objects
that are related to each other are known as aggregates and an aggregate
root is the object that holds them together. Aggregate root is always an
entity. Say for example in a blog application a comment would be related
to a post. So post and comments become an aggregate and post becomes the
aggregate root as there cant be any comments without a post.

Aggregates are to be considered as one unit with regard to any data
manipulation and these aggregates can be accessed from outside only
through their aggregate root. This mechanism of accessing the objects
only by the root helps in maintain data integrity as individual objects
can not be changed directly.

*Factories*

When we are to create an aggregate or an entity, it might become too
complex for us to create it in the constructor of the root entity. This
is where the factory fits in. The role of the factory is to create an
object. These factories are helpful when we are creating an aggregate
because when the root is created, all the associated aggregates under
the root is also created for us.

The process of object creation by a factory needs to be atomic. This is
required because we don’t want to have an aggregate root created without
creating the the associated aggregates which might leave the system in a
unstable state.

You can read about the factory pattern from my earlier posts
[here](http://www.simplyvinay.com/Post/25/Design-Patterns-For-Dummies.-The-Factory-Method-Pattern.aspx)
and
[here](http://www.simplyvinay.com/Post/27/Design-Patterns-for-Dummies.-The-Abstract-Factory-Pattern.aspx).

*Repositories*

In any application, domain objects will need other objects for their
operation. So these domain objects could be made to hold references to
the other needed objects. If we are to give the  domain objects this
behavior of maintaining references, then they become tightly coupled as
they are holding on to all the references they need at any given time.

This could be avoided by the using repositories. Repositories are used
to maintain the references of objects. Using repositories the domain
objects are not involved in maintaining the references to the needed
object, instead they can get it from the repository.

Repositories are just a storage place where we can store the data and
retrieve it later. These repositories are based on strategy, meaning, 
based on different strategies it may use different storage locations for
different type of objects.

*Next Steps*

What I have covered in this and my previous post is just the starting
point of DDD. There are still many factors into DDD, which probably I
will be writing about as I build the application.

In my next post I will be writing about the project structure, why the
project is split as it is, and what is the role of each layer.

