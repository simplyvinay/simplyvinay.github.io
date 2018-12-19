---
layout: post
title: "Aspects Of DDD. Part I"
date: 2009-03-23
comments: true
disqus_identifier: 43
categories: [DDD]
redirect_from: "/Post/43/Aspects-Of-DDD.-Part-I.aspx/"
---
*What is DDD?*

DDD could be called as a methodology or a set of patterns which help us
in developing a software pertaining to the domain. Here what domain
means, is the actual system for which we are building the software. DDD
is not breaking away from the core Object Oriented design principles,
but to utilize them in the right way. There are many aspects in DDD
which we need to consider when building an application. Following are
the aspects and their brief explanations from what I have understood.

*The Ubiquitous Language ( UL )*

Developers when building a software tend to wholly dwell into the
technical aspects of the the application unlike the domain experts who
have an extensive knowledge of the problem domain rather than the
technical solution for it. When a domain expert is sharing his
expertise, he will using a lot of terms that may not be understood by
the development team, thus brining in a communication gap between the
two. 

Ubiquitous language in DDD addresses this communication gap between the
developers and the business experts. The function of the UL is to
connect all parts of the design which would have been led by the domain
experts and developers need to follow this. The key point in UL is to
identify the the concepts which defines the domain and come up with
common terms for those.

*Layered Architecture*

When we develop a software in the OO way, a lot of UI and data specific
code is mixed with the domain objects. The problem with this is a change
in the UI or the data specific code might change the domain behavior and
tracking these changes becomes difficult because to arrive at the domain
behavior we’ll have to check the UI/data components which changed the
behavior in the first place.

The solution for this is to segregate the software into layers such that
the layers are loosely coupled and one layer being dependent only on the
layers below it. The main criteria in the layering is to free up the
domain objects from the UI/data specific logic.

I will talk about the different layers in a future post where I’ll come
up with the initial project structure for the blog application.

*Entities and Value Objects*

Entities and Value Objects form the underlying principles in DDD.
Entities are objects which have an identity and this identity is
maintained throughout the lifetime of the application. The identity can
be represented  differently in different applications, for example an
store application can have an Customer entity which can be identified
from the CustomerID.

Value Objects are objects that have no identity, meaning that we are
representing an object by its attributes alone. Usually Value Objects
acts as a part of an entity, for example the Customer entity can have an
Address Value object which in itself cannot be identified other than
belonging to a customer.

*Services*

When developing a software there are certain behavior which might not
fit into entities or value objects, meaning if we add these behavior
into the objects then they might give a different meaning to the object,
which was not intended. But in OO languages we would need an object to
represent this functionality, These objects are declared as services.
For example in a store application shipping a product to a customer can
be declared as a service.

Services are interfaces which are used in performing some operations on
the objects ( entities / value objects ). These operations are the ones
that doesn't fit in the objects.

This is the the end of Part I. I will continue with the basics of DDD in
my next post.

