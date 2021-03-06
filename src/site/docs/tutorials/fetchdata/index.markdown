---
layout: default
title: "Target 9: Fetch Data Dynamically"
description: "Use HttpRequest to fetch data from a file or a server."
has-permalinks: true
tutorial:
  id: fetchdata
---

{% capture whats_the_point %}

* Data on the web is often formatted in JSON.
* JSON is text based and human readable.
* The dart:json library provides support for JSON.
* Use HttpRequest to dynamically load data.

{% endcapture %}

{% capture content %}

<div id="under-construction" markdown="1">
<h3> <i class="icon-wrench"> </i> Under construction </h3>

This is a draft under construction.
Your kindly worded
<a
 href="http://code.google.com/p/dart/issues/entry?template=Tutorial%20feedback"
 target="_blank">
comments and suggestions
</a>
are appreciated.
Thank you for your patience.
</div>

Web applications often use
<a href="http://www.json.org" target="_blank">JSON</a> (JavaScript Object Notation)
to pass data between clients and servers.
Data can be _serialized_ into a JSON string,
which is then passed between a client and server,
and revived as an object at its destination.
This target shows you how to use functions in the
<a href="http://api.dartlang.org/dart_json.html"
   target="_blank">dart:json</a>
library to produce and consume JSON data.
Because JSON data is typically loaded dynamically,
this target also shows how a web app
can use an HTTP request to get data from an HTTP server.
For web apps,
HTTP requests are served by the browser in which the app is running,
and thus are subject to the browser's security restrictions.

* [About JSON](#about-json)
* [Serializing data into JSON](#serializing-data-into-json)
* [Parsing JSON data](#parsing-json-data)
* [About URIs and HTTP requests](#about-uris)
* [Using the getString() function to load a file](#using-getString-function)
* [Using an HttpRequest object to load a file](#making-a-get-request)

##About JSON

The JSON data format is easy for humans
to write and read because it is lightweight and text based.
With JSON, various data types
and simple data structures such as lists and maps
can be serialized and represented by strings.

Try it! 
The app running below, `its_all_about_you`,
displays the JSON string for data of various types.
Change the values of the input elements
and check out the JSON format for each data type.

<iframe style="border-style:solid;border-width:1px;border-radius:7px;background-color:WhiteSmoke;height:500px;width:700px;padding:5px"
        src="http://dart-lang.github.com/dart-tutorials-samples/web/target09/its_all_about_you/web/out/its_all_about_you.html">
</iframe>

The complete source code is available on github:
<a href="https://github.com/dart-lang/dart-tutorials-samples/tree/master/web/target09/its_all_about_you/web"
   target="_blank">its_all_about_you</a>.
Because this application uses the Web UI package,
you need to compile as described in
<a href="/docs/tutorials/web_ui/">Target 6: Get Started with Web UI</a>.

The dart:json library contains two top-level functions
for working with JSON strings:

| dart:json function | Description |
|---|---|
| <a href="http://api.dartlang.org/dart_json.html" target="_blank">parse()</a> | Builds Dart objects from a string containing JSON data. |
| <a href="http://api.dartlang.org/dart_json.html" target="_blank">stringify()</a> |  Serializes a Dart object into a JSON string. |
{: .table}

The parse() and stringify() functions can handle these Dart types automatically:
<ul>
<li> num</li>
<li> String</li>
<li> bool</li>
<li> null</li>
<li> List</li>
<li> Map</li>
</ul>

Import dart:json using the `as` clause
to give your code clarity
and to avoid naming conflicts.

{% highlight dart %}
import 'dart:json' as json;
{% endhighlight %}

##Serializing data into JSON

Use the stringify() function to serialize an object that supports JSON.
Here's the function from the previous example that
converts all of the data to JSON strings.

![Use stringify() to convert objects into JSON](images/stringify.png)

Below is the JSON string that results from the code
using the original values from the its_all_about_you app.

![The JSON string for the its_all_about_you app](images/jsonstring.png)

Boolean and numeric values
appear as they would if they were literal values in code,
without quotes or other delineating marks.
A boolean value is either `true` or `false`.
A null object is represented with `null`.

Strings are contained within double quotes.
A list is delineated with square brackets;
its items are comma-separated.
The list in this example contains strings.
A map is delineated with curly brackets;
it contains comma-separated key/value pairs,
where the key appears first, followed by a colon,
followed by the value.
In this example,
the keys in the map are strings.
The values in the map vary in type but they are all JSON-parsable.

##Parsing JSON data

Use the parse() function from the dart:json library to
create Dart objects from a JSON string.
The example above initially populates the form from this
JSON string:

{% highlight dart %}
String jsonDataAsString = '''
{ "favoriteNumber":44,
  "valueOfPi":3.141592,
  "chocolate":true,
  "horrorScope":"virgo",
  "favoriteThings":["raindrops",
                    "whiskers",
                    "mittens"]
}
''';

Map jsonData = json.parse(jsonDataAsString);
{% endhighlight %}

This code calls the parse() function with a properly formatted JSON string.
<strong>Note that Dart strings can use either single or double quotes to denote strings.
JSON requires double quotes.</strong>

In this example, the full JSON string is hard coded into the Dart code,
but it could be created by the form itself
or read from a static file or received from a server.
An example later on this page shows how to dynamically fetch
JSON data from a file that is co-located with the code for the app.

The parse() function reads the string and
builds Dart objects from it.
In this example, 
the parse() function creates a Map object based on
the information in the JSON string.
The Map contains objects of various types
including an integer, a double, a boolean value, a regular string,
and a list.
All of the keys in the map are strings.

##About URIs and HTTP requests {#about-uris}

To make an HTTP GET request from within a web app,
you need to provide a URI (Uniform Resource Identifier) for the resource.
A URI (Uniform Resource Identifier) is a character string
that uniquely names a resource.
A URL (Uniform Resource Locator) is a specific kind of URI
that also provides the location of a resource.
URLs for resources on the World Wide Web
contain three pieces of information:

* The protocol used for communication
* The hostname of the server
* The path to the resource

For example, the URL for this page breaks down as follows:

![The tutorial URL](images/uri-details.png)

This URL specifies the HTTP protocol.
At its most basic,
when you enter an HTTP address into a web browser,
the browser sends an HTTP GET request to a web server,
and the web server sends an HTTP response that contains the
contents of the page (or an error message).

![Basic HTTP communication between client and server](images/client-server.png)

Most HTTP requests in a web browser are simple GET requests
asking for the contents of a page.
However, the HTTP protocol allows for other types of requests,
such as POST for sending data from the client.

A Dart web app running inside of a browser can make HTTP requests.
These HTTP requests are handled by the browser in which the app is running.
Even though the browser itself can make HTTP requests anywhere on the web,
a Dart web app running inside the browser can make only *limited*
HTTP requests because of security restrictions.
Practically speaking,
because of these limitations,
HTTP requests from web apps are useful only for
retrieving information in files specific to
and co-located with the app.

<aside class="alert" markdown="1">
<strong>A note about security:</strong>
Browsers place tight security restrictions on HTTP requests
made by embedded apps.
Specifically, any resources requested by a web app
must be served from the same origin.
That is, the resources must be from the same protocol, host, and port
as the app itself.
This means that your web app cannot request
just any resource from the web with HTTP requests through the browser,
even if that request is seemingly harmless (like a GET.)

Some servers do allow cross-origin requests
through a mechanism called
CORS (Cross-origin resource sharing),
which uses headers in an HTTP request
to ask for and receive permission.
CORS is server-specific and not yet widely used.
</aside>

The SDK provides these useful classes for
formulating URIs and making HTTP requests:

| Dart code | Library | Description |
|---|---|
| <a href="http://api.dartlang.org/dart_uri/Uri.html" target="_blank">Uri</a> | dart:uri | An object representing a URI. |
| <a href="http://api.dartlang.org/dart_html/HttpRequest.html" target="_blank">HttpRequest</a> |  dart:html | Client-side HTTP request object. For use in web apps. |
| <a href="http://api.dartlang.org/dart_io/HttpRequest.html" target="_blank">HttpRequest</a> |  dart:io | Server-side HTTP request object. Does not work in web apps. |
{: .table}

##Using the getString() function to load a file {#using-getString-function}

One useful HTTP request your web app *can* make is a GET request
for a data file served from the same origin as the app.
The example below,
`portmanteaux_simple`, includes a data file
called `portmanteaux_simple.json` that contains a JSON-formatted list of words.
When you click the button,
the app makes a GET request of the server
and loads the file.

Try it! Click the button.

<iframe style="border-style:solid;border-width:1px;border-radius:7px;background-color:WhiteSmoke;height:400px;width:300px;padding:5px"
        src="http://dart-lang.github.com/dart-tutorials-samples/web/target09/portmanteaux_simple/web/portmanteaux_simple.html">
</iframe>

This program uses a convenience method,
`getString()`,
provided by the HttpRequest class
to request the file from the server.

![Use the getString() function to make a request](images/getstringfunction.png)

The getString() method uses a Future object to handle the request.
You can use the code above as an idiom
and provide your own code for the body of the processString() function
and your own code to handle the error.

###Using a relative URI

The URI used for the GET request specifies just the name of
the portmanteaux_simple.json data file.
Let's take a look at how that works.

Download and save all of the source files for this program.
All of the files should be together in a single directory.

<ul>
 <li>
<a href="http://raw.github.com/dart-lang/dart-tutorials-samples/master/web/target09/portmanteaux_simple/web/portmanteaux_simple.dart"
   target="_blank">portmanteaux_simple.dart</a>
  </li>
  <li>
<a href="http://raw.github.com/dart-lang/dart-tutorials-samples/master/web/target09/portmanteaux_simple/web/portmanteaux_simple.html"
   target="_blank">portmanteaux_simple.html</a>
  </li>
  <li>
<a href="http://raw.github.com/dart-lang/dart-tutorials-samples/master/web/target09/portmanteaux_simple/web/portmanteaux_simple.css"
   target="_blank">portmanteaux_simple.css</a>
  </li>
  <li>
<a href="http://raw.github.com/dart-lang/dart-tutorials-samples/master/web/target09/portmanteaux_simple/web/portmanteaux_simple.json"
   target="_blank">portmanteaux_simple.json</a>
  </li>
</ul>

Open the application directory in Dart Editor,
select the portmanteaux_simple.html file, and run the program.
Before doing anything else,
notice the URI for the program in Dartium.

![URI for a Dart program running in Dartium](images/uri-dart-program.png)

<ul>
  <li markdown="1">
The server designation 127.0.0.1 is the standard
for referring to _this computer_&mdash;the computer
on which the program is running.
(`localhost` is a human-friendly synonym for 127.0.0.1.)
  </li>

  <li markdown="1">
3030 is a port number.
Because a computer can run multiple servers at once,
to avoid conflicts each server _must_ listen on its own port.
Port numbers from 0 to 1024 are called well-known ports
and are reserved for use by system processes
that provide widely used types of network services.
Typically HTTP servers listen on port 80.
On most systems,
port numbers from 1024 to 49151
are free to be used by any program.
Dart Editor listens on port 3030.
  </li>

  <li markdown="1">
The rest of the URI is the absolute pathname to the HTML file
that hosts the app.
  </li>
</ul>

The HttpRequest object resolves the file name to an absolute URI
using the URI for the current web page as its basis.

![A relative URI resolves to an absolute URI](images/relative-uri.png)

The GET request in this
example is successful because the app and the requested resource
are from the same origin:

![The app and the data file have the same origin](images/same-origin.png)

##Using an HttpRequest object to load a file {#making-a-get-request}

The getString() method is good for an HTTP GET request that returns 
a string loaded from the resource.
For different cases,
you need to create an HttpRequest object,
configure its header and other information,
and use the `send()` method to make the request.
Here is the portmanteaux sample rewritten
to use an explicitly constructed HttpRequest object.

<ul>
 <li>
<a href="http://raw.github.com/dart-lang/dart-tutorials-samples/master/web/target09/portmanteaux/web/portmanteaux.dart"
   target="_blank">portmanteaux.dart</a>
  </li>
  <li>
<a href="http://raw.github.com/dart-lang/dart-tutorials-samples/master/web/target09/portmanteaux/web/portmanteaux.html"
   target="_blank">portmanteaux.html</a>
  </li>
  <li>
<a href="http://raw.github.com/dart-lang/dart-tutorials-samples/master/web/target09/portmanteaux/web/portmanteaux.css"
   target="_blank">portmanteaux.css</a>
  </li>
  <li>
<a href="http://raw.github.com/dart-lang/dart-tutorials-samples/master/web/target09/portmanteaux/web/portmanteaux.json"
   target="_blank">portmanteaux.json</a>
  </li>
</ul>

###Setting up the HttpRequest object

The mouse-click handler for the button
creates an HttpRequest object,
configures it with a URI and callback function,
and then sends the request.
Let's take a look at the Dart code:

![Making an HTTP GET request](images/portmanteaux-code.png)

###Sending the request

The send() method sends the request to the server.

{% highlight dart %}
httpRequest.send('');
{% endhighlight %}

Because the request in this example is a simple GET request,
the code can send an empty string.
For other types of requests,
such as POST requests,
this string can contain further details or relevant data.
You can also configure the HttpRequest object
by setting various header parameters using the setRequestHeader() method.

###Handling the response

To handle the response from the request,
you need to set up a callback function
before calling send().
Our example sets up a one-line callback function
for `onLoadEnd` events
that in turn calls `requestComplete()`.
This callback function is called when the request completes,
either successfully or unsuccessfully.

![Set up a callback function for request completion](images/set-callback.png)

The callback function for our example,
requestComplete(),
checks the status code for the request.
If the status code is 200,
the file was found and loaded successfully,
The contents of the requested file, `portmanteaux.json`, are
returned in the `responseText` property of an HttpRequest object.
Using the `parse()` function from the dart:json library,
the code easily converts the JSON-formatted list of words
to a Dart list of strings,
creates a new LIElement for each one,
and adds it to the &lt;ul&gt; element on the page.

![Getting the response text from an HTTP GET request](images/portmanteaux-callback.png)

###Populating the UI from JSON

The data file in the portmanteaux example,
<a href="http://raw.github.com/dart-lang/dart-tutorials-samples/master/web/target09/portmanteaux/web/portmanteaux.json"
   target="_blank">portmanteaux.json</a>,
contains a JSON-formatted list of strings.

{% highlight dart %}
[
  "portmanteau", "fantabulous", "spork", "smog",
  "spanglish", "gerrymander", "turducken", "stagflation",
  "Brangelina", "freeware", "oxbridge", "palimony",
  "brunch", "blog", "chortle", "Hassenpfeffer", "Schnitzelbank"
]
{% endhighlight %}

Upon request, the server reads this data from the file
and sends it as a single string
to the client program.
The client program receives the JSON string
and uses the parse() function from the dart:json library
to create the String objects specified by the JSON string.

![Parse a JSON formatted list of strings](images/json-parse.png)

<div class="row">
  <div class="span3">
  <a href="/docs/tutorials/custom-elements/"><i class="icon-chevron-left"> </i> Define a Custom DOM Tag</a>
  </div>
  <div class="span3">
<a href="http://code.google.com/p/dart/issues/entry?template=Tutorial%20feedback"
 target="_blank">
<i class="icon-comment"> </i>
Send feedback
</a>
  </div>
  <div class="span3">
  <a href="/docs/tutorials/" class="pull-right">Home <i class="icon-chevron-right"> </i> </a>
  </div>
</div>

{% endcapture %}

{% include tutorial.html %}
