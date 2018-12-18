---
layout: post
title: "MPBlog Implementation. Part 5"
date: 2009-06-23
comments: true
disqus_identifier: 51
categories: [.NET]
---
*Setting up StructureMap and The Unit Of Work ActionFilter*

*UnitOfWork ActionFilter*

In my previous
[post](http://www.simplyvinay.com/Post/50/MPBlog-Implementation.-Part-4.aspx)
I had created the repository implementations along with unit of work.
But how do we use this unit of work in our application. ASP.NET MVC has
a concept of ActionFilters. These could be applied to an action or a
controller in general. The two main methods that concern us are
*OnActionExecuting* and the *OnActionExecuted*. As seen in the code
below we call the initialize method of the unit of work which would
create a new transaction for us and this is held until the action is
complete after which we commit the transaction. We are getting a new
instance of \_unitOfWork through an IoC container which I will talk
about in a bit.

{% highlight csharp %}
namespace MPBlog.Web.UI.ActionFilters
{
    using System;
    using System.Web.Mvc;
    using Core.BaseClasses;
    using StructureMap;

    public class UseUnitOfWOrkAttribute : ActionFilterAttribute
    {
        private readonly IUnitOfWork _unitOfWork;

        public UseUnitOfWOrkAttribute()
        {
            _unitOfWork = ObjectFactory.GetInstance<IUnitOfWork>();
        }

        public UseUnitOfWOrkAttribute(
            IUnitOfWork unitOfWork)
        {
            _unitOfWork = unitOfWork;
        }

        public override void OnActionExecuting(
            ActionExecutingContext filterContext)
        {
            _unitOfWork.Initialize();
        }

        public override void OnActionExecuted(
            ActionExecutedContext filterContext)
        {
            try
            {
                _unitOfWork.Commit();
            }
            catch (Exception)
            {
                _unitOfWork.Rollback();
                throw;
            }
            finally
            {
                _unitOfWork.Dispose();
            }
        }
    }
}
{% endhighlight %}

We use this action filter either by decoration individual action with
the attribute or if we want all actions to use the unit of work, then we
can decorate the class itself with the attribute. In the following code
I am using the *UseUnitOfWork* filter on the Index action.

{% highlight csharp %}
namespace MPBlog.Web.UI.Controllers
{
    using System.Web.Mvc;
    using ActionFilters;
    using Core.Services;

    [HandleError]
    public class HomeController : Controller
    {
        private readonly IBlogService _blogService;

        public HomeController(
            IBlogService blogService)
        {
            _blogService = blogService;
        }

        [UseUnitOfWOrk]
        public ActionResult Index()
        {
            var posts = _blogService.GetAllPosts();
            return View(posts);
        }
    }
}
{% endhighlight %}

Attributes may look a bit intrusive (atleast for me). I like how
[FubuMVC](http://code.google.com/p/fubumvc/) handles this. In
[FubuMVC](http://code.google.com/p/fubumvc/), we have something called
behaviors and this can be configured in the application start. So if we
want **all** our actions to use the unit of work, then we can set the
behavior as shown below and that's it, no more attributes on the action
or controller. In the following code the
*access\_the\_database\_through\_a\_unit\_of\_work* has the same
implementation as the filter we created.

{% highlight csharp %}
ControllerConfig.Configure = x => {
// Default Behaviors for all actions -- ordered as they're executed
 x.ByDefault.EveryControllerAction(d => d
        .Will<access_the_database_through_a_unit_of_work>());
};
{% endhighlight %}

*Services*

Instead of accessing the repository directly from the controllers, I
have created a separate service which delegates the work to the
repository to the operations related. Following is an example of a blog
service. I have kept the methods to be minimal for brevity.

{% highlight csharp %}
namespace MPBlog.Core.Services
{
    using System.Collections.Generic;
    using Domain;

    public interface IBlogService
    {
        IEnumerable<Post> GetAllPosts();
        IEnumerable<Post> GetPostsByTag(string tag);
        
        Post GetPostById(int postID);
        Post GetPostBySlug(string slug);

        void SavePost(Post post);
        void UpdatePost(Post post);
        void DeletePost(Post post);
    }
}
{% endhighlight %}

{% highlight csharp %}
namespace MPBlog.Core.Services
{
    using System.Collections.Generic;
    using System.Linq;
    using BaseClasses;
    using Domain;

    public class BlogService : IBlogService
    {
        private readonly IRepository _repository;

        public BlogService(
            IRepository repository)
        {
            _repository = repository;
        }

        public IEnumerable<Post> GetAllPosts()
        {
            return _repository.Query<Post>().AsEnumerable();
        }

        public IEnumerable<Post> GetPostsByTag(
            string tag)
        {
            return _repository.Query<Tag>(t => t.Description.Equals(tag)).SingleOrDefault().Posts;
        }

        public Post GetPostById(
            int postId)
        {
            return _repository.Load<Post>(postId);
        }

        public Post GetPostBySlug(
            string slug)
        {
            return _repository.Query<Post>(p => p.PostSlug.Equals(slug)).SingleOrDefault();
        }

        public void SavePost(
            Post post)
        {
            _repository.Save(post);
        }

        public void UpdatePost(
            Post post)
        {
            _repository.Save(post);
        }

        public void DeletePost(
            Post post)
        {
            _repository.Delete(post);
        }
    }
}
{% endhighlight %}

*Dependency Injection using StructureMap*

If we look at the all the code that has been shown till now, we see
that, there is almost always an interface declared and we can nowhere
see a direct instantiation of the object that is required. So how do we
get a new instance of an object. This is where an Inversion Of Control
(IoC/DI)  container comes into play. Basically what it does is to give
you a concrete objects whenever we require.

I have used constructor injection where ever possible. How it works is,
by defining our own ControllerFactory and create the controller
ourselves instead of ASP.NET MVC doing it for us. In the below code I
have a StrucutreMapControllerFactory from where I am getting an instance
of the controller. This factory inherits from the
DefaultControllerFactory for ASP.NET MVC and we override the controller
building method.

{% highlight csharp %}
namespace MPBlog.Web.UI.Controllers
{
    using System;
    using System.Web.Mvc;
    using StructureMap;

    public class StructureMapControllerFactory : DefaultControllerFactory
    {
        protected override IController GetControllerInstance(
            Type controllerType)
        {
            IController result = null;
            if (controllerType != null)
            {
                try
                {
                    result = ObjectFactory.GetInstance(controllerType) as Controller;
                }
                catch (StructureMapException)
                {
                    System.Diagnostics.Debug.WriteLine(ObjectFactory.WhatDoIHave());
                    throw;
                }
            }

            return result;
        }
    }
}
{% endhighlight %}

We set this controller factory in our Application\_Start method in the
global.asax file like so.

{% highlight csharp %}
RegisterRoutes(RouteTable.Routes);
Bootstrapper.Bootstrap();
ControllerBuilder.Current.SetControllerFactory(new StructureMapControllerFactory());
{% endhighlight %}

Now that we have controllerfactory ready, we can configure our
dependencies. The way to configure our dependency is through a registry
which provides a fluent interface. The following code shows all the
dependencies being loaded into the container through the
MPBlogRegistry. 

{% highlight csharp %}
namespace MPBlog.Web.UI
{
    using StructureMap;

    public class Bootstrapper : IBootstrapper
    {
        public void BootstrapStructureMap()
        {
            ObjectFactory.Initialize(x => x.AddRegistry(new MPBlogRegistry()));
        }

        public static void Bootstrap()
        {
            new Bootstrapper().BootstrapStructureMap();
        }
    }
}
{% endhighlight %}

{% highlight csharp %}
using System.Configuration;

namespace MPBlog.Web.UI
{
    using Core.BaseClasses;
    using Core.Services;
    using FluentNHibernate;
    using Persistence;
    using Persistence.Config;
    using Persistence.RepositoryImpl;
    using Persistence.UnitOfWork;
    using StructureMap.Attributes;
    using StructureMap.Configuration.DSL;

    public class MPBlogRegistry : Registry
    {
        public MPBlogRegistry()
        {
            ForRequestedType<ISessionSource>().AsSingletons().TheDefault.Is.ConstructedBy(
                context => context.GetInstance<ISessionSourceConfiguration>()
                    .CreateSessionSource(new MPBlogPersistenceModel()));
            ForRequestedType<ISessionSourceConfiguration>().AsSingletons().TheDefault.Is
                .OfConcreteType<SQLSessionSourceConfiguration>().WithCtorArg("connectionString")
                .EqualTo(ConfigurationManager.ConnectionStrings["MPBlog"].ConnectionString).WithCtorArg("resetDb")
                .EqualToAppSetting("reset");
            ForRequestedType<IUnitOfWork>().TheDefault.Is
                .ConstructedBy(context => context.GetInstance<INHibernateUnitOfWork>());
            ForRequestedType<INHibernateUnitOfWork>().CacheBy(InstanceScope.Hybrid).TheDefault.Is
                .OfConcreteType<NHibernateUnitOfWork>();
            ForRequestedType<IRepository>().AsSingletons().TheDefault.Is.OfConcreteType<NHRepository>();
            ForRequestedType<IBlogService>().AsSingletons().TheDefault.Is.OfConcreteType<BlogService>();
        }
    }
}
{% endhighlight %}

Once this is done whenever we ask for a instance of an object, the IoC
toll will lookup and give us the concerned concrete object.

Thats it for now.

