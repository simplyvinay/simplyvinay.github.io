---
layout: post
title: "Type Inference, Anonymous types and Lambda Expressions"
date: 2008-10-01
comments: true
disqus_identifier: 8
categories: [C#]
---
This is the second of posts about the new features in C# 3.0. You can
read the first post
[here](/Post/6/Automatic-Properties-and-Object-Initializers.aspx). Well
this was long overdue, but anyway, here it is.

**Type Inference**

When we are using type inference which was introduced in C# 3.0, the
compiler determines the type of the variable at compile time. Here is an
example of type inference.

{% highlight csharp %}
public class TypeInference
{
    var globalStr = "Hello C# 3.0"; //Compile time error.
                                    //The contextual keyword 'var' may
                                    //only appear within a local variable declaration

    public void TypeInferenceMethod()
    {
        var msg = "Hello C# 3.0"; //string
        var offset = 0; // int
        var temp; //Compile time error. Implicitly-typed local variables must be initialized
        temp = "Error";
    }
}
{% endhighlight %}

Here the variable msg will be of type string and offset will be of type
int. You have to note that these variables have to be initialized and
can only be used as local variables or else you will get a compile time
error as is the case with globalStr and temp.

**Anonymous types** 

Anonymous types are useful when we need to create an object of a class
and initialize it without having a concrete class. Let me explain this
with an example.

{% highlight csharp %}
public class AnonymousType
{
    public void AnonymousTypeMethod()
    {
        var product = new {Id = 1, Name = "Product1"};
    }
}
{% endhighlight %}

Here we create a type product with properties Id an Name. The good thing
about this code is that there is no Product class present. The variable
product does have a type though but we don't know its name. We just get
a class with properties Id and Name already initialized. 

**Lambda Expressions**

Lambda expressions are a refined way of defining your methods. Anonymous
methods were introduced in C# 2.0 which helped us to declare a method
inline which was much simpler than the earlier version ( will show an
example shortly ). Then came lambdas in C# 3.0 which took anonymous
methods to a different level. Lets look at an example of all 3 forms.

{% highlight csharp %}
class Lambdas
{
    //1. Using delegates
    static void UsingDelegate()
    {
        List<int> intList = new List<int> {1, 2, 4, 5, 6};
        Predicate<int> getEvenNumbers = new Predicate<int>(IsEven);
        List<int> evenNumbers = intList.FindAll(getEvenNumbers);
    }

    static bool IsEven(
        int i)
    {
        return (i % 2) == 0;
    }

    //2. Using anonymous methods
    static void UsingAnonymousMethod()
    {
        List<int> intList = new List<int> {1, 2, 4, 5, 6};
        List<int> evenNumbers = intList.FindAll(
            delegate(
                int i)
            {
                return (i % 2) == 0;
            });
    }

    //3. Using lambdas
    static void UsingLambdas()
    {
        List<int> intList = new List<int> {1, 2, 4, 5, 6};
        List<int> evenNumbers = intList.FindAll(i => (i % 2) == 0);
    }
}
{% endhighlight %}

Here I have written 3 methods which basically creates an integer list
and finds out all even numbers in it. The first method is done using the
traditional delegate pattern. Here we create a predicate pointing to a
method which checks for a number to be even. Then we call the FindAll
method for the list and pass this predicate to get the list of even
numbers. As you can see the IsEven method might not be used outside the
scope of this method. To solve this issue C# introduced anonymous
methods. In anonymous methods we can declare the method inline as can be
seen the example. This saves us from writing a different method. When
you look at the third method we see that a single line of code is doing
all the work. In fact  i => (i % 2) == 0 is all that is needed to do
the functionality that our predicate or delegate did. Lambda expression
are internally converted into anonymous delegates. Just a quicker and
neat way of doing things.

