---
layout: post
title: "MPBlog Implementation. Part 3"
date: 2009-05-11
comments: true
disqus_identifier: 49
categories: [.NET]
redirect_from: "/Post/49/MPBlog-Implementation.-Part-3.aspx/"
---
*Mapping The Classes And Session Source Configuration*

As mentioned in my previous
[post](/2009/04/15/A-Brief-Intermission/),
this series will no longer be a talk about DDD as a blog app is too
trivial to show ( learn ) the power of it. I will be using some of the
patterns of DDD though. So I have changed the name of the app I am
building. It will be called MyPersonalBlog ( MPBlog ) henceforth.

The project structure has changed a bit from what I had shown
[here](/2009/04/06/DDDBlog-Implementation.-Part-1/).
There is no longer separate layers for infrastructure and domain. I have
clubbed these 2 layers into a single Core layer. The other layers are
just the same.

Lets get started by creating our domain objects.

*The domain objects*

So far we have created a entity base class from which all our domain (
models ) will inherit from. In a blog application, we’ll have posts,
tags and comments. So lets create classes for each of these. I have
created these classes as shown in the class diagram below.

[![ClassDiagram](/assets/img/2009-05-11/ClassDiagram.png "ClassDiagram")](/assets/img/2009-05-11/ClassDiagram.png)

From the diagram you can see that there is a many-to-many relation
between Tag and Post class..

*Domain mappings*

I am using [Fluent NHibernate](http://fluentnhibernate.org/) to
configure the mappings of these classes to the database. Although you
can map all the classes using the
[automapping](http://wiki.fluentnhibernate.org/show/AutoMapping) feature
which has a convention based API, I will use the mapping per class
feature. Lets look at the mappings.

*PostMap*

{% highlight csharp %}
namespace MPBlog.Persistence.DomainMap
{
    using Core.Domain;
    using FluentNHibernate.Mapping;

    public class PostMap : ClassMap<Post>
    {
        public PostMap()
        {
            WithTable("Posts");
            Id(
                p => p.Id,
                "PostId").WithUnsavedValue(0).Access.AsReadOnlyPropertyThroughCamelCaseField().GeneratedBy.Identity();
            Map(p => p.PostTitle).Not
                .Nullable();
            //will be created as nvarchar(max)
            Map(p => p.PostText).Not.Nullable().WithLengthOf(4001);
            Map(p => p.ExcerptText).Not.Nullable().WithLengthOf(1000);
            Map(p => p.PostSlug).Unique();
            Map(p => p.AddedBy).Not.Nullable();
            Map(p => p.PublishDate);
            // Many to many with an intermediate table ( PostsToTags )
            HasManyToMany(p => p.Tags).WithTableName("PostsToTags").Cascade.SaveUpdate();
            HasMany(p => p.Comments).WithTableName("Comments").Cascade.All().Inverse();
        }
    }
}
{% endhighlight %}

We create a PostMap class which inherits from the ClassMap\<T\>. The
constructor of this PostMap class is where all out mappings go. As can
be seen, first we specify the table name for our Posts, Then we specify
the Id property which is mapped to the PostId column in our database.
The Id in our case is the id that we had declared in the Entity base
class ( I have renamed the Key property to Id ). If you recall from my
previous
[post](/2009/04/08/DDDBlog-Implementation.-Part-2/),
it had only the getter property. The value is assigned by NHibernate. We
specify a many to many relation between posts and tags through an
intermediate table PostToTags. We have also specified that a post has
many comments thought the Comments table The other mappings are self
explanatory.

Out Tag and Comment mapping classes look similar to the one above.
Following are the two mapping files for these.

*TagMap*

{% highlight csharp %}
namespace MPBlog.Persistence.DomainMap
{
    using Core.Domain;
    using FluentNHibernate.Mapping;

    public class TagMap : ClassMap<Tag>
    {
        public TagMap()
        {
            WithTable("Tags");
            Id(
                t => t.Id,
                "TagId").WithUnsavedValue(0).Access.AsReadOnlyPropertyThroughCamelCaseField().GeneratedBy.Identity();
            Map(t => t.Description).Not.Nullable();
            Map(t => t.CreatedDate);
            HasManyToMany(t => t.Posts).WithTableName("PostsToTags").Cascade.SaveUpdate();
        }
    }
}
{% endhighlight %}

*CommentMap*

{% highlight csharp %}
namespace MPBlog.Persistence.DomainMap
{
    using Core.Domain;
    using FluentNHibernate.Mapping;

    public class CommentMap : ClassMap<Comment>
    {
        public CommentMap()
        {
            WithTable("Comments");
            Id(c => c.Id).WithUnsavedValue(0).Access.AsReadOnlyPropertyThroughCamelCaseField().GeneratedBy.Identity();
            Map(c => c.Body).Not.Nullable();
            Map(c => c.AuthorName).Not.Nullable();
            Map(c => c.AuthorIP).Not.Nullable();
            Map(c => c.AuthorEmail).Not.Nullable();
            Map(c => c.AuthorUrl).Not.Nullable();
            Map(c => c.AddedDate).Not.Nullable();
            References(c => c.Post).Not.Nullable().Cascade.All();
        }
    }
}
{% endhighlight %}

Now that our mappings are done, lets create a SessionSource to access
the NHibernate Session.

We can create a session through the ISessionSource interface in Fluent
NHibernate. Lets create an interface for the SessionSourceConfiguration.

*Note:* The following is based on the FubuMVC contrib
[project](http://code.google.com/p/fubumvc-contrib/).

*ISessionSourceConfiguration and *SessionSourceConfiguration*

{% highlight csharp %}
namespace MPBlog.Persistence.Config
{
    using FluentNHibernate;

    public interface ISessionSourceConfiguration
    {
        bool ResetDatabase { get; }

        ISessionSource CreateSessionSource(
            PersistenceModel model);
    }
}
{% endhighlight %}

{% highlight csharp %}
namespace MPBlog.Persistence.Config
{
    using System.Collections.Generic;
    using FluentNHibernate;
    using FluentNHibernate.Cfg.Db;

    public class SessionSourceConfiguration : ISessionSourceConfiguration
    {
        private readonly string _connectionString;
        public bool ResetDatabase { get; private set; }

        public SessionSourceConfiguration(
            string connectionString,
            bool resetDb)
        {
            _connectionString = connectionString;
            ResetDatabase = resetDb;
        }

        public ISessionSource CreateSessionSource(
            PersistenceModel model)
        {
            var properties = GetProperties(_connectionString);
            var source = new SessionSource(
                properties,
                model);
            CreateDbWithSchema(source);
            return source;
        }

        public IDictionary<string, string> GetProperties(
            string connectionString)
        {
            return MsSqlConfiguration.MsSql2005.ConnectionString(c => c.Is(connectionString)).UseOuterJoin().ShowSql()
                .ToProperties();
        }

        private void CreateDbWithSchema(
            ISessionSource source)
        {
            if (ResetDatabase)
            {
                source.BuildSchema();
            }
        }
    }
}
{% endhighlight %}

The ResetDatabase property is used to either create a new database from
our mappings or reset the database. We have a method called
GetProperties from which we generate the connection properties.

As you can see in the CreateSessionSource method, we are passing a
PersistenceModel object. This object is used to add the mapping from a
specified assembly, in our case, where we have defined the Post, Tag and
Comment maps. Lets call it MPBlogPersistenceModel. Here is what it looks
like.

{% highlight csharp %}
using FluentNHibernate;

namespace MPBlog.Persistence
{
    public class MPBlogPersistenceModel : PersistenceModel
    {
        public MPBlogPersistenceModel()
        {
            addMappingsFromThisAssembly();
        }
    }
}
{% endhighlight %}

The PresistenceModel object, connectionString, and resetDB parameters
are injected using an IoC container which we’ll have a look in a later
post.

Following are the tests I have written to check my mapping are correct.
I am using an in memory SQLite database and the
PersistenceSpecification\<T\> provided by Fluent NHibernate to check my
mapping.

*Mapping Tests*

{% highlight csharp %}
namespace MPBlog.Tests.Persistence.Tests
{
    using System;
    using Core.Domain;
    using FluentNHibernate;
    using FluentNHibernate.Cfg.Db;
    using FluentNHibernate.Testing;
    using MPBlog.Persistence;
    using NHibernate;
    using Xunit;

    public class PersistenceTests
    {
        private ISession session;

        public PersistenceTests()
        {
            var persistenceModel = new MPBlogPersistenceModel();
            var config = new SQLiteConfiguration().InMemory()
                .ConnectionString(c => c.Is("Data Source=:memory:;Version=3;New=True;")).ShowSql();
            var sessionSource = new SessionSource(
                config.ToProperties(),
                persistenceModel);
            session = sessionSource.CreateSession();
            sessionSource.BuildSchema(session);
        }

        [Fact]
        public void Post_mapping_test()
        {
            new PersistenceSpecification<Post>(session).CheckProperty(
                p => p.AddedBy, "vinay").CheckProperty(
                p => p.PostTitle, "title").CheckProperty(
                p => p.PostText, "text").CheckProperty(
                p => p.ExcerptText, "text").CheckProperty(
                p => p.PostSlug, "slug").CheckProperty(
                p => p.PublishDate, DateTime.Today).VerifyTheMappings();
        }

        [Fact]
        public void Tag_mapping_test()
        {
            new PersistenceSpecification<Tag>(session).CheckProperty(
                t => t.Description, "tag1").CheckProperty(
                t => t.CreatedDate, DateTime.Today).VerifyTheMappings();
        }

        [Fact]
        public void Comment_mapping_test()
        {
            new PersistenceSpecification<Comment>(session).CheckProperty(
                c => c.AuthorEmail, "vinay@simplyvinay.com").CheckProperty(
                c => c.AuthorIP, "127.0.0.1").CheckProperty(
                c => c.AuthorName, "vinay").CheckProperty(
                c => c.AuthorUrl,"www.simplyvinay.com").CheckProperty(
                c => c.Body, "comment").CheckProperty(
                c => c.AddedDate, DateTime.Today).CheckReference(
                c => c.Post, new Post
                {
                    AddedBy = "vinay", PostTitle = "title", PostText = "text", ExcerptText = "text", PostSlug = "slug",
                    PublishDate = DateTime.Today
                }).VerifyTheMappings();
        }
    }
}
{% endhighlight %}

That's it for now. In my next post, I’ll discuss about the repository
and the unit of work implementations. Any sort of feedback will be
appreciated.

