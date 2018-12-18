---
layout: post
title: "DDDBlog Implementation. Part 2"
date: 2009-04-08
comments: true
disqus_identifier: 47
categories: [DDD]
---
In my previous [post](/2009/04/06/DDDBlog-Implementation.-Part-1/), we looked at setting up the project structure and
the tools and libraries that we’ll need to build the application. In
this post we’ll look at creating a few base classes.

*Layer SuperType: The Entity Base Class*

Before we start of creating the domain objects, lets first create the
base entity object. As discussed before, an
[entity](/2009/03/23/Aspects-Of-DDD.-Part-I/)
is something that has an identity. This would be our layer supertype
because this class will be inherited by all our domain objects that
needs to have an identity.

{% highlight csharp %}
namespace DDDBlog.Infrastructure.BaseClasses
{
    using System;

    public interface IEntity
    {
        int Key { get; }
    }

    public abstract class Entity : IEntity
    {
        private int key;

        public int Key
        {
            get { return key; }
        }

        protected Entity()
        {
        }

        protected Entity(
            int key)
        {
            this.key = key;
        }

        public bool Equals(
            Entity other)
        {
            return Equals(
                this,
                other);
        }

        public override bool Equals(
            object obj)
        {
            return Equals(
                this,
                obj as Entity);
        }

        public static bool Equals(
            Entity obj1,
            Entity obj2)
        {
            if (Object.Equals(
                    obj1,
                    null) || Object.Equals(
                    obj2,
                    null) || obj1.GetType() != obj2.GetType()) return false;
            if (ReferenceEquals(
                obj1,
                obj2)) return true;
            return obj1.Key == obj2.Key;
        }

        public override int GetHashCode()
        {
            return Key.GetHashCode();
            ;
        }
    }
}
{% endhighlight %}

The Entity base class is defined in the Infrastructure layer as the
definition of entity is not part of the domain itself but it will be
used by the domain. As you can see the Entity class has a key property
which would be used as an identity. Also I am taking the key to be
integer for simplicity. I have also implemented the equality comparison
on the entity object. This is used to compare the entities.

*Entity Tests*

Now lets see some tests on the Entity class. A thing to remember is that
in TDD, we would have to write the tests firsts and then come at the
object.

{% highlight csharp %}
namespace DDDBlog.Tests.Infrastructure.Tests
{
    using DDDBlog.Infrastructure.BaseClasses;
    using Xunit;

    public class FakeEntity : Entity
    {
        public FakeEntity() : base(0)
        {
        }

        public FakeEntity(
            int key) : base(key)
        {
        }
    }

    public class EntityTests
    {
        [Fact]
        public void Can_Create_An_Entity()
        {
            var entity = new FakeEntity();
            Assert.NotNull(entity);
        }

        [Fact]
        public void Newly_Created_Entity_Id_Should_Be_Zero()
        {
            var entity = new FakeEntity();
            Assert.Equal(
                entity.Key,
                0);
        }

        [Fact]
        public void Two_Entities_With_Same_Key_Should_Be_Equal()
        {
            var entity1 = new FakeEntity(21);
            var entity2 = new FakeEntity(21);
            Assert.Equal(
                entity1,
                entity2);
            Assert.Equal(
                entity1.GetHashCode(),
                entity2.GetHashCode());
        }

        [Fact]
        public void Two_Entities_With_Diffrent_Key_Should_Not_Be_Equal()
        {
            var entity1 = new FakeEntity(21);
            var entity2 = new FakeEntity(3);
            Assert.NotEqual(
                entity1,
                entity2);
            Assert.NotEqual(
                entity1.GetHashCode(),
                entity2.GetHashCode());
        }

        [Fact]
        public void Reference_To_Same_Entity_Should_Be_Equal()
        {
            var entity1 = new FakeEntity(21);
            var entity2 = entity1;
            Assert.Equal(
                entity1,
                entity2);
            Assert.Equal(
                entity1.GetHashCode(),
                entity2.GetHashCode());
        }
    }
}
{% endhighlight %}

*AggregateRoot And IRepository\<T\>*

Along with the Entity base class, we also need a repository base, and a
way to mark an entity as an aggregate root. These are shown in the
following snippets

{% highlight csharp %}
namespace DDDBlog.Infrastructure.BaseClasses
{
    public interface IAggregateRoot : IEntity
    {
    }
}
{% endhighlight %}

{% highlight csharp %}
namespace DDDBlog.Infrastructure.Repository
{
    using System.Linq;
    using BaseClasses;

    public interface IRepository<T> where T : IAggregateRoot
    {
        T FindBy(
            int key);

        IQueryable<T> FindAll();

        void Save(
            T entity);

        void Delete(
            T entity);
    }
}
{% endhighlight %}

As you can see our Aggregate root base is just an interface that
inherits from IEntity. This is done because a repository is ( should be
) provided only on the Aggregate root. You can read a bit on Aggregates
and Repositories
[here](/2009/03/25/Aspects-Of-DDD.-Part-II/).

Now that our base classes are ready, we can look at the domain objects
of our sample application, which I will be talking about in the next
post.

You must know that, I am by no means an expert in DDD, TDD. The code
shown above might be wrong as I am just stepping into this part of the
world. Please leave a comment/ suggestion if you find something to be
wrong or if it could be done better.

