---
layout: post
title: "MPBlog Implementation. Part 4"
date: 2009-05-18
comments: true
disqus_identifier: 50
categories: [.NET]
redirect_from: "/Post/50/MPBlog-Implementation.-Part-4.aspx/"
---
*NHibernate Repository and Unit Of Work*

In my previous [post](/2009/05/11/MPBlog-Implementation.-Part-3/) we
looked at the domain model and the class maps associated with it. In
this post lets look at the Repository and Unit Of Work implementations.

*Note:* The following is based on the FubuMVC contrib
[project](http://code.google.com/p/fubumvc-contrib/).

*IRepository*

A repository is an abstraction layer which gives your application an in
memory domain object collection. This layer helps in making you
application agnostic about where the data is coming from or where it is
persisted into. It could well be a flat file or any other RDBMS.
Preferably each of your domain object would have its own repository. But
for the sake of keeping it simple, I am using a generic repository in
the following code.
<!--more-->
{% highlight csharp %}
namespace MPBlog.Core.BaseClasses
{
    using System;
    using System.Linq;
    using System.Linq.Expressions;

    public interface IRepository
    {
        T Load<T>(int id) where T : Entity<T>;
        void Delete<T>(T target);
        void Save<T>(T target);

        IQueryable<T> Query<T>() where T : Entity<T>;

        IQueryable<T> Query<T>(
            Expression<Func<T, bool>> whereQuery) where T : Entity<T>;
    }
}
{% endhighlight %}

The Query methods here will be implemented using [Linq to
NHibernate](http://sourceforge.net/projects/nhcontrib/). Now that we
have the interface, lets look at the implementation.

*NHRepository*

{% highlight csharp %}
namespace MPBlog.Persistence.RepositoryImpl
{
    using System;
    using System.Linq;
    using System.Linq.Expressions;
    using Core.BaseClasses;
    using NHibernate.Linq;
    using UnitOfWork;

    public class NHRepository : IRepository
    {
        private INHibernateUnitOfWork _unitOfWork;

        public NHRepository(
            INHibernateUnitOfWork unitOfWork)
        {
            _unitOfWork = unitOfWork;
        }

        public T Load<T>(
            int id) where T : Entity<T>
        {
            return _unitOfWork.Session.Load<T>(id);
        }

        public void Delete<T>(
            T target)
        {
            _unitOfWork.Session.Delete(target);
        }

        public void Save<T>(
            T target)
        {
            _unitOfWork.Session.Save(target);
        }

        public IQueryable<T> Query<T>() where T : Entity<T>
        {
            return _unitOfWork.Session.Linq<T>();
        }

        public IQueryable<T> Query<T>(
            Expression<Func<T, bool>> whereQuery) where T : Entity<T>
        {
            return _unitOfWork.Session.Linq<T>().Where(whereQuery);
        }
    }
}
{% endhighlight %}

If you notice the above code, we are accessing the *session*through the
unitofwork. A unit of work helps in keeping track of all the objects
that are affect by a business transaction. Following is the
implementation of the UnitOfWork.

*IUnitOfWork and INHibernateUnitOfWork*

{% highlight csharp %}
namespace MPBlog.Core.BaseClasses
{
    using System;

    public interface IUnitOfWork : IDisposable
    {
        void Initialize();
        void Commit();
        void Rollback();
    }
}
{% endhighlight %}

{% highlight csharp %}
namespace MPBlog.Persistence.UnitOfWork
{
    using Core.BaseClasses;
    using NHibernate;

    public interface INHibernateUnitOfWork : IUnitOfWork
    {
        ISession Session { get; }
    }
}
{% endhighlight %}

*NHibernateUnitOfWork*

{% highlight csharp %}
namespace MPBlog.Persistence.UnitOfWork
{
    using System;
    using FluentNHibernate;
    using NHibernate;

    public class NHibernateUnitOfWork : INHibernateUnitOfWork
    {
        private ITransaction _transaction;
        private readonly ISessionSource _source;
        private bool _isDisposed;
        private bool _isInitialized;
        public ISession Session { get; private set; }

        public NHibernateUnitOfWork(
            ISessionSource source)
        {
            _source = source;
        }

        public void Initialize()
        {
            CheckUoWSanity();
            Session = _source.CreateSession();
            StartNewTransaction();
            _isInitialized = true;
        }

        public void Commit()
        {
            CheckUoWSanity();
            CheckUoWInitialization();
            _transaction.Commit();
            StartNewTransaction();
        }

        public void Rollback()
        {
            CheckUoWSanity();
            CheckUoWInitialization();
            _transaction.Rollback();
            StartNewTransaction();
        }

        public void Dispose()
        {
            if (_isDisposed || !_isInitialized) return;
            _transaction.Dispose();
            Session.Dispose();
            _isDisposed = true;
        }

        private void CheckUoWSanity()
        {
            if (_isDisposed)
            {
                throw new ObjectDisposedException("Trying to use disposed object");
            }
        }

        private void CheckUoWInitialization()
        {
            if (!_isInitialized)
            {
                throw new InvalidOperationException("NHibernateUnitOfWork is not initialized");
            }
        }

        private void StartNewTransaction()
        {
            if (_transaction != null)
            {
                _transaction.Dispose();
            }

            _transaction = Session.BeginTransaction();
        }
    }
}
{% endhighlight %}

*Tests*

Following are the test that were written for both the repository and
unitofwork. I am using [MoQ](http://code.google.com/p/moq/) as my
mocking framework so that I don't have to connect to the actual database
when testing.

{% highlight csharp %}
namespace MPBlog.Tests.Persistence.Tests
{
    using System.Linq;
    using Core.Domain;
    using Moq;
    using MPBlog.Persistence.RepositoryImpl;
    using MPBlog.Persistence.UnitOfWork;
    using NHibernate;
    using Xunit;

    public class RepositoryTests
    {
        private Mock<ISession> _session;
        private Mock<INHibernateUnitOfWork> _uow;
        private Mock<NHRepository> _repo;

        public RepositoryTests()
        {
            _session = new Mock<ISession>();
            _uow = new Mock<INHibernateUnitOfWork>();
            _uow.Setup(u => u.Session).Returns(_session.Object);
            _repo = new Mock<NHRepository>(_uow.Object);
        }

        [Fact]
        public void Call_to_load_should_load_object_from_the_session()
        {
            var postId = 1;
            _repo.Object.Load<Post>(postId);
            _session.Verify(
                s => s.Load<Post>(postId),
                Times.AtLeastOnce());
        }

        [Fact]
        public void Call_to_save_should_save_object_on_the_session()
        {
            var post = new Post();
            _repo.Object.Save(post);
            _session.Verify(
                s => s.SaveOrUpdate(post),
                Times.AtLeastOnce());
        }

        [Fact]
        public void Call_to_delete_should_delete_object_from_the_session()
        {
            var post = new Post();
            _repo.Object.Delete(post);
            _session.Verify(
                s => s.Delete(post),
                Times.AtLeastOnce());
        }

        [Fact]
        public void Call_to_query_on_session_should_return_an_IQueryable_object()
        {
            Assert.IsAssignableFrom(
                typeof(IQueryable<Post>),
                _repo.Object.Query<Post>());
        }
    }
}
{% endhighlight %}

{% highlight csharp %}
namespace MPBlog.Tests.Persistence.Tests
{
    using FluentNHibernate;
    using Moq;
    using MPBlog.Persistence.UnitOfWork;
    using NHibernate;
    using Xunit;

    public class UnitOfWorkTests
    {
        protected Mock<ISession> _session;
        protected Mock<ITransaction> _transaction;
        protected Mock<ISessionSource> _sessionSource;
        protected Mock<NHibernateUnitOfWork> _uow;

        public UnitOfWorkTests()
        {
            _session = new Mock<ISession>();
            _transaction = new Mock<ITransaction>();
            _sessionSource = new Mock<ISessionSource>();
            _sessionSource.Setup(s => s.CreateSession()).Returns(_session.Object);
            _session.Setup(s => s.BeginTransaction()).Returns(_transaction.Object);
            _uow = new Mock<NHibernateUnitOfWork>(_sessionSource.Object);
            _uow.Object.Initialize();
        }

        [Fact]
        public void Can_create_new_transaction()
        {
            _session.Verify(
                s => s.BeginTransaction(),
                Times.AtLeastOnce());
        }

        [Fact]
        public void Can_dispose_the_transaction()
        {
            _uow.Object.Dispose();
            _transaction.Verify(
                s => s.Dispose(),
                Times.Exactly(1));
        }

        [Fact]
        public void Can_dispose_the_session()
        {
            _uow.Object.Dispose();
            _session.Verify(
                s => s.Dispose(),
                Times.Exactly(1));
        }

        [Fact]
        public void Call_to_commit_should_commit_the_transaction()
        {
            _uow.Object.Commit();
            _transaction.Verify(
                t => t.Commit(),
                Times.AtLeastOnce());
        }

        [Fact]
        public void Call_to_commit_should_dispose_the_transaction_and_start_a_new_transaction()
        {
            _uow.Object.Commit();
            _transaction.Verify(
                t => t.Dispose(),
                Times.Exactly(1));
            _session.Verify(
                s => s.BeginTransaction(),
                Times.Exactly(2));
        }

        [Fact]
        public void Call_to_rollback_should_rollback_the_transaction()
        {
            _uow.Object.Rollback();
            _transaction.Verify(
                t => t.Rollback(),
                Times.AtLeastOnce());
        }

        [Fact]
        public void Call_to_rollback_should_dispose_the_transaction_and_start_a_new_transaction()
        {
            _uow.Object.Rollback();
            _transaction.Verify(
                t => t.Dispose(),
                Times.Exactly(1));
            _session.Verify(
                s => s.BeginTransaction(),
                Times.Exactly(2));
        }
    }
}
{% endhighlight %}

