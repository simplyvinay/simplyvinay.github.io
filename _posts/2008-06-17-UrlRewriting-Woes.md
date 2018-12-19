---
layout: post
title: "UrlRewriting Woes"
date: 2008-06-17
comments: true
categories: [MPS]
redirect_from: "/Post/1/UrlRewriting-Woes.aspx/"
---
After spending a very long time to build this site, i finally put it in
production. The prime reason for the delay was hosting which i was not
able to afford up until now. 

Well everything seemed to work fine on my dev machine but once i put the
code in production, my URL rewriting logic failed. I was using Gaidar
Magdanurov's
[UrlRewiting](http://www.simple-talk.com/dotnet/asp.net/a-complete-url-rewriting-solution-for-asp.net-2.0/)
solution. From what i observed the http module was not picking up on the
application load. So as a last minute change i used
[UrlRewritingNet.UrlRewrite](http://www.urlrewriting.net/). I did not
have to change much of the thing for this to work. Its pretty straight
forward to setup and use.

First you have to add a config section

{% highlight csharp %}
<configSections>
	<section name="urlrewritingnet" requirePermission="false" type="UrlRewritingNet.Configuration.UrlRewriteSection, UrlRewritingNet.UrlRewriter"/>
</configSections>
{% endhighlight %}

The next step is to define the http module ( This is where i think my
other solution fails)

{% highlight csharp %}
<httpModules>
	<add name="UrlRewriteModule" type="UrlRewritingNet.Web.UrlRewriteModule, UrlRewritingNet.UrlRewriter"/>
</httpModules>
{% endhighlight %}

Now you are ready to write the rules like so.

{% highlight csharp %}
<urlrewritingnet rewriteOnlyVirtualUrls="true" contextItemsPrefix="QueryString" defaultPage="default.aspx" defaultProvider="RegEx" xmlns="http://www.urlrewriting.net/schemas/config/2006/07">
	<rewrites>
		<add name="Post" virtualUrl="Post/(d+)/(.*).aspx" rewriteUrlParameter="ExcludeFromClientQueryString" destinationUrl="ShowBlog.aspx?ID=$1" ignoreCase="true"/>
	</rewrites>
</urlrewritingnet>
{% endhighlight %}

Well you would guess that this is all you do and everything works fine.
Even i thought so, but when i was testing this, i logged into my site
and the URL which i expected to be something like this
"http://vinay.qsh.eu/Post/1/something.aspx" had become
"http://vinay.qsh.eu/Post/1/something.aspx?ID=1". This broke my page
because there are 2 parameters with the same id. It beats me as to why
this is being added on login/logout. It is not a postback issue as every
other postback is working fine.

How to fix this? This is what i have done.

Add a helper method to check whether if the URL is rewritten one or
plain URL

{% highlight csharp %}
public static bool URLRewritten(
    string LocalPath)
{
    bool ruleFound = false;
    foreach (UrlRewritingNet.Configuration.RewriteSettingsrewriteSettings in UrlRewritingNet.Web.UrlRewriting
        .Configuration.Rewrites)
    {
        UrlRewritingNet.Web.RewriteRule rewrite = null;
        string providerName = rewriteSettings.Provider;
        if ((string.IsNullOrEmpty(providerName)))
        {
            rewrite = UrlRewritingNet.Web.UrlRewriting.CreateRewriteRule();
        }
        else
        {
            rewrite = UrlRewritingNet.Web.UrlRewriting.CreateRewriteRule(providerName);
        }

        rewrite.Initialize(rewriteSettings);
        if ((rewrite.IsRewrite(LocalPath)))
        {
            ruleFound = true;
            break;
        }
    }
    return ruleFound;
}
{% endhighlight %}

Then use this function in the LoggedIn and LoggedOut events to manage
the URL

{% highlight csharp %}
protected void Login_LoggedIn(
    object sender,
    EventArgs e)
{
    if (Helpers.URLRewritten(Request.Url.LocalPath))
    {
        if (Request.Url.PathAndQuery.Contains("ReturnUrl"))
            Response.Redirect(Request.Params["ReturnUrl"].ToString());
        else
            Response.Redirect(Request.Url.LocalPath);
    }
    else
    {
        if (Request.Url.PathAndQuery.Contains("ReturnUrl"))
            Response.Redirect(Request.Params["ReturnUrl"].ToString());
        else
            Response.Redirect(Request.Url.PathAndQuery);
    }
}

protected void LoginStatus1_LoggedOut(
    object sender,
    EventArgs e)
{
    if (Helpers.URLRewritten(Request.Url.LocalPath))
    {
        Response.Redirect(Request.Url.LocalPath);
    }
    else
    {
        Response.Redirect(Request.Url.PathAndQuery);
    }
}
{% endhighlight %}

One more thing. To manage the Return URL, you'll have to call the helper
function like so

{% highlight csharp %}
if (Helpers.URLRewritten(Request.Url.LocalPath))
{
    Response.Redirect(FormsAuthentication.LoginUrl + "?ReturnUrl=" + Request.Url.LocalPath);
}
else
{
    Response.Redirect(FormsAuthentication.LoginUrl + "?ReturnUrl=" + Request.Url.PathAndQuery);
}
{% endhighlight %}

I will be using this workaround until i find a fix.

