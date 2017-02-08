---
layout: post
title: "Day 2 - Taking a Look at the Visual Studio Templates for .NET Core"
excerpt: "Learn about the Visual Studio Templates for .NET Core in this mini-series"
tags: [edge, windows, chrome, aspnet, dotnetcore, uwp]
share: true
comments: true
---

Disclaimer: *I am **not** on the .NET Core Team*. I used the tools available publicly.

## Intro

So far in this mini-series, I've walked you through [Installing .NET Core on a Windows box and learning some commands and creating a Console application](http://michaelcrump.net/getting-started-with-aspnetcore/). In this post, I'm going to walk you through the Visual Studio templates for .NET Core. 

## VS Templates and funky references

Before proceeding make sure that you install .NET Tools for Visual Studio from the  [downloads page](https://www.microsoft.com/net/download/core). 

Go to File -> New Project and look for .NET Core to create a project in Visual Studio. Select Console Application from the list below: 

![image](/files/dotnetcorevstemplates.png)

Once the project loads, you will see the following structure which is very similar to most Console apps with the exception being the way **References** is displayed.

![image](/files/netcoresolutionexplorer.png)

If we add `Console.WriteLine("Hello World");` to the Main method, then we don't see any red squiggly lines. This is because System.Console is part of the .NET Standard Library that is included in this template as shown below:

![image](/files/consoledotnetcore.png)

You can browse out to the [source code](https://github.com/dotnet/corefx/tree/master/src) to see foundation class libraries in .NET Core. You can also check out [Package search](http://packagesearch.azurewebsites.net/) to help you find .NET Core class libraries. 

## What if you need to add a package reference that isn't available by default?

Let's setup a situation where the package isn't included and we'll need to add it. I'll use a quick and dirty sample of Json.NET. 

Create a file named Account.cs and drop this in: 

{% highlight csharp %}

	public class Account
    {
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public string Email { get; set; }
        public IList<string> Roles { get; set; }
    } 

{% endhighlight %}

And add the following to your Main method in Program.cs

	Account account = new Account
    {
         Email = "michael@blah.com",
         Active = true,
         CreatedDate = new DateTime(2017, 2, 20, 0, 0, 0, DateTimeKind.Utc),
         Roles = new List<string>
         {
              "User",
              "Admin"
         }
     };

     string json = JsonConvert.SerializeObject(account, Formatting.Indented);
     Console.WriteLine(json);

Simple enough! The JsonConvert call and Formatting should have a red squiggly line. You can now simply add the package as shown below: 

![image](/files/jsonnotworking.png)

Nice! It has been added. You can look inside project.json and see it being referenced as shown below. 

	{
	  "version": "1.0.0-*",
	  "buildOptions": {
	    "emitEntryPoint": true
	  },
	
	  "dependencies": {
	    "Microsoft.NETCore.App": {
	      "type": "platform",
	      "version": "1.0.1"
	    },
	    "Newtonsoft.Json": "9.0.1"
	  },
	
	  "frameworks": {
	    "netcoreapp1.0": {
	      "imports": "dnxcore50"
	    }
	  }
	}

The thing to keep in mind here is that you could have actually added the dependency reference directly in the project.json file as shown below:

![image](/files/newtonsoft.gif)

This is very similar to Node.js if you are familiar with that. 

## Wrap-up

I'll call it a day with that. As always, thanks for reading and smash one of those share buttons to give this post some love if you found it helpful. Also, feel free to leave a comment below if there is something that you want to know as I learn .NET Core.