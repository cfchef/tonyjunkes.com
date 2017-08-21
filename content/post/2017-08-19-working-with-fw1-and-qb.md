---
title: "Working With FW/1 & QB"
subtitle: "Steps to Integrate Using Subsystems"
slug: "working-with-fw1-and-qb"
date: 2017-08-19
draft: true
tags: ["CFML","FW1","QB"]
---

Today I wanted to go over a library that I think is pretty awesome and echoes capabilities found in many other modern languages: QB.

[QB](https://www.forgebox.io/view/qb) is a query builder written by [Eric Peterson](https://github.com/elpete), who's been putting out some [really cool modular libraries](https://www.forgebox.io/user/elpete) for the CFML community.

With QB, you can:

- Quickly scaffold simple queries
- Make complex, out-of-order queries possible
- Abstract away differences between database engines

The syntax uses a builder pattern that makes writing queries more readable and easy to understand when initially glancing through code.

As simple as:

```
query.from("posts").get();
```

And complex as:

```
query.from("posts")
     .whereNotNull("PublishDate")
     .whereIn("AuthorID", [1,2,3])
     .get();
```

QB is written to integrate with ColdBox but in this post, I'll go over how to integrate it as a subsystem in an FW/1 application and mimic the WireBox dependencies with FW/1's baked in dependency injection library, DI/1. While FW/1 and ColdBox have many differences in feature set, their convention to MVC patterns and syntax is still quite relatable.

> If you're every glancing through ForgeBox and see a standalone library that's built to integrate with ColdBox, integrating it in your FW/1 app might (emphasis on might!) be easier than you think; as I'll demonstrate below.

## Getting Started

### Prerequisites

_I'm going to assume you have a fair grasp of FW/1, configuring DI/1 in an FW/1 application & and basic knowledge on how to use FW/1's subsystems feature_. If not, no worries. You'll still be able to follow along and I'll do my best at explaining and pointing out documentation when necessary.

In my examples I'll be using Lucee and an H2 database using MySQL dialect; but you can use whatever engine/db combo you'd like. H2 is an in memory database that is easy to set up in a Lucee Application.cfc without having to touch the admin settings. _Setting up the H2 driver in Adobe ColdFusion may differ as I've never tried._

You'll want a basic FW/1 application fleshed out. Something that follows the standard conventions like so...

```
| project_root
|- controllers
|- layouts
|- views
|- model
|- views
|-- main
|--- default.cfm
|--- error.cfm
|- Application.cfc
|- index.cfm
```

> Some convention based directories will not be used in my examples such as controllers &amp; layouts.

You're framework settings should capture something like this to start:

```
variables.framework = {
    defaultSection: "main",
    defaultItem: "default",
    error: "main.error",
    diEngine: "di1",
    diLocations: "/model",
    subsystems: { },
    trace: true,
    reloadApplicationOnEveryRequest: true
};
```
