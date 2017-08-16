---
title: "Getting Started With Jsoup & CFML"
slug: "getting-started-with-jsoup-and-cfml"
date: 2017-08-16
draft: true
---

Over the years I've made repeatable use of the jsoup library so I figured it'd be nice to put out a little primer of using it with CFML.

### What Is jsoup?

From the official site:

> [jsoup](https://jsoup.org/) is a Java library for working with real-world HTML. It provides a very convenient API for extracting and manipulating data, using the best of DOM, CSS, and jquery-like methods.
>
> jsoup is designed to deal with all varieties of HTML found in the wild; from pristine and validating, to invalid tag-soup; jsoup will create a sensible parse tree.

jsoup allows you to do such things as:

> - Scrape and parse HTML from a URL, file, or string
> - Find and extract data, using DOM traversal or CSS selectors
> - Manipulate the HTML elements, attributes, and text
> - Clean user-submitted content against a safe white-list, to prevent XSS attacks
> - Output tidy HTML

### Getting Started

#### Installing With CommandBox

#### Installing Manually

To manually install jsoup, you can simply go to the [official site download page](https://jsoup.org/download) and pull down the latest `core library` release.

From there you can either drop the JAR into the `/lib` directory of your CFML engine that's added to the class path...

> Note: Paths may vary.
>
> - For Adobe ColdFusion: `your_cf_install/lib`.
> - For Lucee: `your_lucee_site/WEB-INF/lucee-server/lib`

Or you can add the JAR to a directory of your choice and map it in your project's `Application.cfc` via `this.javaSettings`.

```
this.javaSettings = {loadPaths: ["./your_dir"]};
```

To learn more on integrating 3rd party Java libraries in CFML, check out the [CFDocs Java Integration Guide](https://cfdocs.org/java).

```
<cfscript>

	// Create Jsoup object
	Jsoup = createObject("java", "org.jsoup.Jsoup");
	// Create some markup...
	html = '<html><head><title>Hello World!</title></head><body><h1>A Header</h1><p>Some content. <a href="##">A link.</a></p></body></html>';
	// Parse it into a Jsoup Document
	doc = Jsoup.parse(html);

	// Create a Node object
	TextNode = createObject("java", "org.jsoup.nodes.TextNode");
	Node = createObject("java", "org.jsoup.nodes.Node");
	replace = TextNode.init("Steal this link!", "");

	writeDump(replace);
	writeDump(doc.select("a")[1].replaceWith(replace));
	writeDump(doc.text());
	writeDump(doc.body().toString());


<cfscript>
```


Examples
1- Parsing a HTML string
In the first example, we are going to parse a HTML string.

```
<cfscript>
	Jsoup = createObject("java", "org.jsoup.Jsoup");

	html = "<html><head><title>My title</title></head><body>Body content</body></html>";
    Jsoup.parse(html);
	title = doc.title();
	body = doc.body().text();

	writeOutput("Title: #title#");
	writeOutput("Body:<br>#body#");
<cfscript>
```

html = "<html><head><title>My title</title></head><body>Body content</body></html>";

This string contains simple HTML data.

doc = Jsoup.parse(html);

With the Jsoup's parse() method, we parse the HTML string. The method returns an HTML document.

title = doc.title();

The document's title() method gets the string contents of the document's title element.

body = doc.body().text();

The document's body() method returns the body element; its text() method gets the text of the element.

2- Parsing a local HTML file
In the second example, we are going to parse a local HTML file. We use the overloaded Jsoup.parse() method that takes a File object as its first parameter.
```
<!-- HTML file -->
<!DOCTYPE html>
<html>
    <head>
        <title>My title</title>
        <meta charset="UTF-8">
    </head>
    <body>
        <div id="mydiv">Contents of a div element</div>
    </body>
</html>
```
For the example, we use the above HTML file.

```
<cfscript>
	Jsoup = createObject("java", "org.jsoup.Jsoup");
	JFile = createObject("java", "java.io.File");

	fileName = "path/to/file.html";
	doc = Jsoup.parse(JFile.init(fileName), "utf-8");
	divTag = doc.getElementById("mydiv");

	writeOutput(divTag.text());
<cfscript>
```

In this example:

   doc = Jsoup.parse(JFile.init(fileName), "utf-8");

We parse the HTML file with the Jsoup.parse() method.

divTag = doc.getElementById("mydiv");

With the document's getElementById() method, we get the element by its ID.

 writeOutput(divTag.text());

The text of the tag is retrieved with the element's text() method.

3- Reading a web site's title
In the following example, we scrape and parse a web page and retrieve the content of the title element.

```
<cfscript>
	Jsoup = createObject("java", "org.jsoup.Jsoup");

	webPage = "http://www.tonyjunkes.com";
	doc = Jsoup.connect(webPage).get();
	title = doc.title();

	writeOutput(title);
<cfscript>
```

In the code example, we read the title of a specified web page.

doc = Jsoup.connect(webPage).get();

The Jsoup's connect() method creates a connection to the given URL. The get() method executes a GET request and parses the result; it returns an HTML document.

title = doc.title();

With the document's title() method, we get the title of the HTML document.

4- Reading HTML source
The next example retrieves the HTML source of a web page.

```
<cfscript>
	Jsoup = createObject("java", "org.jsoup.Jsoup");

	webPage = "http://www.tonyjunkes.com";
	html = Jsoup.connect(webPage).get().html();

	writeOutput(html);
<cfscript>
```

The example prints the HTML of a web page.

html = Jsoup.connect(webPage).get().html();

The html() method returns the HTML of an element; in our case the HTML source of the whole document.

5- Getting meta information
Meta information of a HTML document provides structured metadata about a Web page, such as its description and keywords.

```
<cfscript>
	Jsoup = createObject("java", "org.jsoup.Jsoup");

	webPage = "http://www.jsoup.org";
	document = Jsoup.connect(webPage).get();
	description = document.select("meta[name=description]").first().attr("content");
	keywords = document.select("meta[name=keywords]").first().attr("content");

    writeOutput("Keywords : #keywords#");
<cfscript>
```

The code example retrieves meta information about a specified web page.

 keywords = document.select("meta[name=keywords]").first().attr("content");

The document's select() method finds elements that match the given query. The first() method returns the first matched element. With the attr() method, we get the value of the content attribute.

5- Parsing links
The next example parses links from an HTML page.

```
<cfscript>
	Jsoup = createObject("java", "org.jsoup.Jsoup");

	webPage = "http://www.jsoup.org";
	document = Jsoup.connect(webPage).get();
	links = document.select("a[href]");

	links.each(function(link) {
		writeOutput("link : #link.attr("href")#");
		writeOutput("text : #link.text()#");
	});
<cfscript>
```

In the example, we connect to a web page and parse all its link elements.

links = document.select("a[href]");

To get a list of links, we use the document's select() method.

6- Sanitizing HTML data
Jsoup provides methods for sanitizing HTML data.

```
<cfscript>
	Jsoup = createObject("java", "org.jsoup.Jsoup");
	Whitelist = createObject("java", "org.jsoup.safety.Whitelist");
	Cleaner = createObject("java", "org.jsoup.safety.Cleaner");

	html = "<html><head><title>My title</title></head><body><center>Body content</center></body></html>";
	valid = Jsoup.isValid(html, Whitelist.basic());

	if (valid) {
		writeOutput("The document is valid");
	} else {
		writeOutput("The document is not valid.");
		writeOutput("Cleaned document");

		dirtyDoc = Jsoup.parse(html);
		cleanDoc = Cleaner.init(Whitelist.basic()).clean(dirtyDoc);

		writeOutput(cleanDoc.html());
	}
<cfscript>
```

In the example, we sanitize and clean HTML data.

 html = "<html><head><title>My title</title></head><body><center>Body content</center></body></html>";


The HTML string contains the center element, which is deprecated.

 valid = Jsoup.isValid(html, Whitelist.basic());

The isValid() method determines whether the string is a valid HTML. A white list is a list of HTML (elements and attributes) that can pass through the cleaner. The Whitelist.basic() defines a set of basic clean HTML tags.

dirtyDoc = Jsoup.parse(html);
cleanDoc = new Cleaner.init(Whitelist.basic()).clean(dirtyDoc);


With the help of the Cleaner, we clean the dirty HTML document.

7- Grabs All Images
This example shows you how to use the Jsoup regex selector to grab all image files (png, jpg, gif) from my company website “x-hub.io”.

```
<cfscript>
	Jsoup = createObject("java", "org.jsoup.Jsoup");
	try {
		//get all images
		doc = Jsoup.connect("http://x-hub.io").get();
		images = doc.select("img[src~=(?i)\\.(png|jpe?g|gif)]");
		images.each(function(image) {
			writeOutput("<br>src : #image.attr("src")#");
			writeOutput("height : #image.attr("height")#");
			writeOutput("width : #image.attr("width")#");
			writeOutput("alt : #image.attr("alt")#");
		});
	}
	catch (any e) {
		writeDump(e);
	}
<cfscript>
```

7- Get form attributes in html page
Getting form input element in a webpage is very simple. Find the FORM element using unique id; and then find all INPUT elements present in that form.

```
<cfscript>
	doc = Jsoup.connect("http://x-hub.io").get();
	formElement = doc.getElementById("contactForm");  
	inputElements = formElement.getElementsByTag("input");

	inputElements.each(function(inputElement) {  
		key = inputElement.attr("name");  
		value = inputElement.attr("value");  
		writeOutput("Param name: #key#<br>Param value: #value#");  
	});
<cfscript>
```