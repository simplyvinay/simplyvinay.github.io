---
layout: post
title: "Logging using Interceptors in Windsor Container"
date: 2008-06-19
comments: true
disqus_identifier: 2
categories: [Castle Project]
redirect_from: "/Post/2/Logging-using-Interceptors-in-Windsor-Container.aspx/"
---
The Windsor container gives you a provision of adding behavior to
components without having to change the components implementation. For
example if you want to log whenever a method is called through your
services class say CustomerService, you can assign an Interceptor for
this particular component and add behavior when this component is
called. Lets see how logging can be achieved for the CustomerService
component.

First thing is to create a LogInterceptor.

{% highlight csharp %}
public class LogInterceptor : IInterceptor
{
    public ILogger Logger;

    public LogInterceptor()
    {
    }

    public void Intercept(
        IInvocation invocation)
    {
        //Get the method name
        string methodName = invocation.GetConcreteMethod().Name;
        Log("Entering method: " + methodName);
        invocation.Proceed();
        Log("Leaving mehod: " + methodName);

    }

    private void Log(
        string log)
    {
        Logger.Info(log);

    }
}
{% endhighlight %}

Here we are implementing the Intercept method of the IInterceptor
interface. We are just getting the method name and logging it before the
method is invoked and after its completion.

Here is the CustomerService class.

{% highlight csharp %}
public class CustomerService : ICustomerService
{
    CustomerDAO custDAO = new CustomerDAO();

    public CustomerService()
    {
    }

    public virtual List<Customer> GetCustomers()
    {
        return custDAO.RetrieveCustomers();
    }
}
{% endhighlight %}

The GetCustomers method just calls the DAO object to get a list of
customers. Next we define the components in the services.config file so
that the Interceptor is injected at runtime.

{% highlight csharp %}
<configuration>
  <components>
    <component id="LogInterceptor" type="Rails.Services.LogInterceptor, Services" lifestyle="transient" />
    <component id="service.customer" type="Rails.Services.CustomerService, Services" service="Rails.Interfaces.ICustomerService, Interfaces">
      <interceptors>
        <interceptor>${LogInterceptor}</interceptor>
      </interceptors>
    </component>
  </components>
</configuration>
{% endhighlight %}

Here we are adding a interceptor to the CustomerService class after
defining the Interceptor.

Thats that. Now whenever the method is called for this particular
component, it is automatically logged.



