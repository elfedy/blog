title : Asynchronous Programming in JavaScript - Part 3 - HTTP Requests

----
XmlHttpRequests
Example with json
Example with image or other resource?
Say something about the Fetch API
Lazy Loading | Videos | Images
----

Apart from reacting to browser events. Interacting with servers by making HTTP requests and using that data to modify the page without reloading it is another important part of asynchronouts JavaScript programming. In particular, this type of programming is called AJAX, Asynchrounous JavaScript and XML, a name with more historical than practical meaning, what we can get from it is that we can use a JS object (XMLHttpRequest) to interact with servers and receive information in several formats, most of interest is the reception of information in JSON, which can be interpreted as a JS object.

## The XMLHttpRequest object
The `XMLHttpRequest` object is the basic data structure used for Ajax.

To do a request we have to:

1) Open a XMLHttpRequest object

```
request = new XMLHttpRequest
```

2) We add listeners that handle what happens when the request is made

```
request.addEventListener("load", handler)
```

3) We call open() with options for the request, HTTP Verb, url, headers???

```
request.open("GET", "http://www.example.org/example.txt")
```

4) We call send() to send the request
request.send()

When the repsonse comes back to the server, the handler will be executed

Disect the XMLHttpRequest object
https://developer.mozilla.org/en-US/docs/Web/Guide/AJAX/Getting_Started
https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest
https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/HTML_in_XMLHttpRequest
https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest



