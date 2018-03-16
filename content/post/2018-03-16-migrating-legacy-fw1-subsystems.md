---
title: "Migrating Legacy FW/1 Subsystems"
slug: "migrating-legacy-fw1-subsystems"
date: 2018-03-16
draft: false
tags: ["CFML","FW1"]
---

Today I'm going to go over how to upgrade the modular capabilities of your crusty old (read: beautifly written) FW/1 application. While you may have been keeping the framework library up to date, perhaps you didn't take the plunge and move your "legacy" arcitecture over. That'll be our target. If you're still running on top of anything pre-v3.5, this will not apply to you; however, I highly recommend you upgrade to the latest and greatest. One major version release at a time.

In 2015, the release of [FW/1 3.5](http://framework-one.github.io/blog/2015/10/21/fw1-3-5-0-released/) brought us `Subsystems 2.0`.

> "A new, more streamlined way to add subsystems to an existing FW/1 application".

This was a big deal to me. As a heavy user of subsystems and a fan of modularity, this not only made the architecture more organized but it also setup super simple and much more user friendly. /Sales pitch ;)

## The Old (Legacy) Way

The original way of working with subsystems in FW/1 involved essentially everything being a subsystem; including a common location for shared layouts and views.

Consider this application structure:

```
legacy_project
|- /admin
|-- /controllers
|--- main.cfc
|-- /layouts
|--- default.cfm
|-- /views
|--- /main
|---- default.cfm
|- /common
|-- /layouts
|--- default.cfm
|- /framework
|- /home
|-- /controllers
|--- main.cfc
|-- /layouts
|--- default.cfm
|-- /views
|--- /main
|---- default.cfm
|---- error.cfm
|- Application.cfc
|- index.cfm
```

So what do we have here?

`/home` is our base (core) of our application. In an FW/1 application not using subsystems, this would potentially be the meat of our app. In this case, it's also being treated as a subsystem. It contains all of the usual base app items - controllers, views, layouts etc.

`/admin` is a subssytem within our application. Any other subsystem can link to it, borrow from it or whatever prupose it built from it. The structure will mimic `/home` but it can act on it's own within our application.

`/common` is the "shared" source for layouts and views among all subsystems in the application. It is also treated as a subsystem.

Everything else should be relatively self-explanitory as it follows the standard conventions of any normal FW/1 application.

Now let's look at the configuration settings in `Application.cfc` that allow all of this to come together...

```
component extends="framework.one" {
    // Application settings (this scope)...

	// FW/1 settings
	variables.framework = {
		action = "action",
		defaultSection = "main",
		defaultItem = "default",
		usingSubsystems = true,
		defaultSubsystem = "home",
		siteWideLayoutSubsystem = "common",
		error = "home.error",
		generateSES = false,
		SESOmitIndex = false,
		diEngine = "di1",
		diComponent = "framework.ioc"
	};

    // Lifecycle functions go here...
}
```

There are 3 main config settings that will make the above structure function.

- usingSubsystems [boolean] : As the name suggests, this is the flag that tells FW/1 whether or not your application is using subsystems.
- defaultSubsystem [string] : This defines the "core" of your application. It's often a starting point for a shared model or "home page".
- siteWideLayoutSubsystem [string] : The name of the folder that houses shared layouts/views to be used as defaults for any susbsystem.

With this understanding under our belt, lets move on to switching to the current, modern way of using subsystems.

## The New [2.0] Way

