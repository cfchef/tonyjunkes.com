---
title: "FW/1 Alternative Application Structure"
slug: "fw1-alternative-application-structure"
date: 2017-11-13
draft: true
tags: ["CFML","FW1"]
---

So I figured I'd have a look at FW/1's "[alternative application structure](http://framework-one.github.io/documentation/4.1/developing-applications.html#alternative-application-structure)" and share my way of how to implement it.

## What Is It?

Basically this structure allows you to abstract the FW/1 lifecycle methods and configuration settings into a separate component. Essentially leaving your Application.cfc in a more "traditional" state besides the calls needed for FW/1.

From the docs:

> It creates the FW/1 instance explicitly on each request and delegates the various lifecycle methods to FW/1. The framework configuration structure must be passed to the FW/1 constructor, instead of being set in variables scope.

## The Benefits

#### Application Mappings & Best Practices

Assuming you are keeping your application code out of the webroot (which you should!), you would need a server level mapping set in the administrator to make FW/1 available to the project. Lucee users can avoid this by passing a path in Application.cfc like `extends="../path/to/fw1"`; however this is not the case with ColdFusion.

This structure allows you to avoid having to set a server level mapping from the administrator or utilize engine specific features. Instead you can define an application mapping in your Application.cfc to point to the component that extends FW/1.

## Example Application

You can find [example files](https://github.com/framework-one/fw1/tree/develop/framework) that relate to the docs in the FW/1 GitHub repo; but I'm going to be laying things out a little differently.

#### Getting Started

##### FW/1 Structure

All we need to start is a typical structure for FW/1 and touch on specific code after. In my example I will use `/src` for the application source code and `/app` as the webroot.

```
fw1-lifecycle-example
|- /app
|-- Application.cfc
|-- index.cfm
|- /src
|-- /controllers
|-- /layouts
|-- /views
|--- /main
|---- default.cfm
|---- error.cfm
```

##### CommandBox All The Things

I'm going to use CommandBox to run my example so I need to create, in the project root, a `box.json` to get FW/1 and a `server.json` to specify the webroot. This way all I'll need to do is run `box install && start` and we're off to the races.

_box.json_
```
{
    "name":"fw1-lifecycle-example",
    "dependencies":{
        "fw1":"be"
    },
    "installPaths":{
        "fw1":"src\\framework"
    }
}
```

_server.json_
```
{
    "name":"fw1-lifecycle-example",
    "web":{
        "webroot":"app",
        "rewrites":{
            "enable":true
        }
    }
}
```
#### The Lifecycle

There's 2 key parts here to making this FW/1 application structure. There's the Application.cfc for creating the FW/1 instance and there's what I've decided to call the "Bootstrap" component that extends FW/1 to set framework settings and call its lifecycle methods.
