---
layout: post
title: "DDDBlog Implementation. Part 1"
date: 2009-04-06
comments: true
disqus_identifier: 46
categories: [DDD,.NET]
redirect_from: "/Post/46/DDDBlog-Implementation.-Part-1.aspx/"
---
In my pervious posts, I wrote about the aspects of TDD and DDD a bit,
and following up on the series, I will start building the sample
application from this post onwards. For the lack of better name, I will
be calling the application DDDBlog.

*Setting up the project*

Lets create the project structure as shown below.
<!--more-->

[![solution](/assets/img/2009-04-06/solution.png "solution")](/assets/img/2009-04-06/solution.png)

*DDDBlog.Domain* : Domain layer is where the Plain-Old CLR Objects
reside. These POCOs are entities that form the domain objects for our
project. This layer would also contain the repository interface
declarations for the domain objects. From a DDD perspective, the Domain
layer should be unaware of persistence ( *Persistence Ignorance* ), so
this layer will not have references to the Persistence layer

*DDDBlog.Infrastructure* : Infrastructure layer provides the bridging
for other layers involved in the application. This layer will be used by
all other layers. This layer would contain all the base classes required
for all other projects. This layer would also define the top level
repository interface. I am not sure where the mapping files should fall
under this layer or the persistence layer, but for now I think I will go
with this layer.

*DDDBlog.Persistence* :  This layer would contain the actual data access
code. Here I will write the actual implementations for the repositories.

*DDDBlog.Tests* : This layer consists all the tests for all the layers.
Its not necessary for us to have single test layer. We can add
individual test layer for each of the layers in our application. I
however am going with a single test layer.

*DDDBlog.Web* : This is the web presentation layer for our application.

*Tools and libraries used*

[ASP.NET MVC](http://www.asp.net/mvc) – Used for the presentation layer.
I had thoughts of using Monorail, but preferred ASP.NET MVC as it has
come a long way from the CTP’s and I think its pretty good as of now.

[NHibernate](http://www.hibernate.org/343.html) and
[FluentNHibernate](http://fluentnhibernate.org/) – These are used for
persistence and for the mappings.

[StructureMap](http://structuremap.sourceforge.net/Default.htm) – This
is used for Dependency Injection.

[xUnit.Net](http://www.codeplex.com/xunit) – This is used for unity
testing.

[MoQ](http://code.google.com/p/moq/) – This is used for mocking.

In this post we have seen how to layer our application and the
tools/libraries that’s needed for our project. In my next post we’ll
start coding the base classes for our application.

