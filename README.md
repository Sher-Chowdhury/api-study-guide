# api-study-guide

## HTTP Request
A HTTP request is made up of 4 parts:

1. URI - this is just a path to resources, e.g. "api.github.com/users/sher-chowdhury"
2  Query string (part of the URI) - this is something that can be added to the end of a uri, they should always be optional. they are often used for formatting, sorting, or searching,...etc. 
3. Verb - what action to take. 
4. Header - Information about the request
5. Content - Content that is asked for


Also use Postman - https://www.postman.com/ - this provides a UI that let's you easily generate your curl commands. 
Useful tool for experimenting with 
- https://httpbin.org/
- https://petstore.swagger.io/#/
- https://mockapi.io/ (this is really good!)



Every HTTP request must always contain a Verb and Header section, but the Content part is not provided in `GET` and `DELETE` request types. As per the spec, you're not allowed to specify what resource(s) you want to get in the Content, instead that has to be specified in the "header" or URI section. Note a [URL is a type of URI](https://www.javatpoint.com/uri-vs-url#:~:text=A%20URI%20is%20a%20sequence,resource%20available%20on%20the%20internet.). 

the `Content` part is actually optional, and depends on the type of request. E.g. you don't provide a content section for `GET` and `DELETE` requests. 




### Verb
There are a few types:

- GET - get a resource
- POST - create a resource
- PUT - update a resource, but can also create a resource (this resource already exists, but just needs partial update)
- PATCH - partial update an existing resource (this isn't used that much)
- DELETE - delete a resource. 
- [There are a few more verbs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods), but the above ones are the most common. 


The difference between `PUT` and `POST` is the `PUT` is idempotent: calling it once or several times successively has the same effect (that is no side effect), whereas successive identical POST requests may have additional effects, akin to placing an order several times. 

I think the difference between `PUT` and `PATCH`, is that with PUT, you have to first do a get to get the whole resource object, then edit it, then PUT it. Whereas with a PUT, you don't do a GET first, you just do make the udpate, e.g. if you have a "persoon", and want to add a new key-value to that person called "species", e.g. "species: 'human'"



### Query Strings

this is something that can be added to the end of a uri, they should always be optional. they are often used for formatting, sorting, or searching,...etc. e.g. 

```
/users?sort=name
```



It can also be used to information that can be passed via the header section, e.g. specify what format the response should be, e.g. 

```
/users?format=json
```

But that is considered bad practice, since the Header section's "Accept" is specifically designed for this use case. 

### Headers
[This contains metadata](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers) about the request in the form of key-value pairs, here are some of the commonly used header keys:

- [Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type) - It specifies what type of data the content is holding. e.g. is it plain text, json object, xml, binary...etc. 
- [Content-Length](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Length)
- [Authorization](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization) - this contains creds
- [Accept](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept) - This tells the web server what types of data I can accept as the response body.
- [Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cookie) - This is data being sent with the request, that the web browser also wants the web server to pass back, in order for the browser to keep track of state. 
- [There are hundreds of other headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)

You can also define your own custom headers too. 

### Content

This can be anything, e.g. json object, html, css, javascript, .png files, .jpeg files,....etc. 

Remember, you should not provide a content section in certain request types, e.g. `GET` requests, as stated here - https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET




## HTTP Response

A HTTP Response is made up of 3 parts:

1. Status code - e.g. 200
2. Header
3. Content


### Headers
Similar to Request headers, but wanted to point out some common ones:

- Expires - when to consider something is stale
- Cookies - this returns the same cookies it received in the original http request, but optionally, the response could append additional data in the cookie. The server then expects those additional cookie data to be passed back to it in subsequent calls. 


### Content 

same as request's coThis can be anything, e.g. json object, html, css, javascript, .png files, .jpeg files,....etc. 
ntent section. 

Note, when you submit a POST to create a resource, then the HTTP response's content should return the entire resource, including generated info such as unique ID, createdAt date...etc. 




## Curl Examples

The following shows the HTTP repsonse's content section

```
$ curl arest.me
<!DOCTYPE html>
<html>
<head>
  <title>Arest.me</title>
  <link href="/css/site.css" rel="stylesheet" />
</head>
<body>
  <h1>This is just a web page.</h1>
</body>
</html>%
```

The following shows the HTTP repsonse's status code + header section only, by using the -I flag:

```
$ curl -I arest.me
HTTP/1.1 200 OK                                 <= status code section is just this one line. The rest is the header's key-values
Content-Type: text/html; charset=utf-8
Date: Sun, 30 Apr 2023 11:17:36 GMT
Server: Kestrel
Cache-Control: public,max-age=600
ETag: "37AF4AA9213FEFD72DDE04B714DE94D8"
Expires: Sun, 30 Apr 2023 11:27:36 GMT
Last-Modified: Sun, 30 Apr 2023 11:17:36 GMT
Vary: Accept, Accept-Language, Accept-Encoding
```

You need to use `-i` to see the entire HTTP response, i.e. all three parts, the status-code, headers, and content:

```
$ curl -i arest.me
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Date: Sun, 30 Apr 2023 11:17:32 GMT
Server: Kestrel
Age: 334
Cache-Control: public,max-age=600
ETag: "37AF4AA9213FEFD72DDE04B714DE94D8"
Expires: Sun, 30 Apr 2023 11:27:32 GMT
Last-Modified: Sun, 30 Apr 2023 11:17:32 GMT
Transfer-Encoding: chunked
Vary: Accept, Accept-Language, Accept-Encoding

<!DOCTYPE html>
<html>
<head>
  <title>Arest.me</title>
  <link href="/css/site.css" rel="stylesheet" />
</head>
<body>
  <h1>This is just a web page.</h1>
</body>
</html>%
```



## What is REST
REST stands for "Representational State Transfer"

RESTful - this means a pragmatic way of using REST, meaning that it's not 100% but very close to it as-musch-as-possible. 

Key concepts of REST:
- seperattion of client and server. I think that means no active connection/tunnel, like when creating a ssh connection.  
- All server requests are stateless - the server responds to a http requests by providing a http response, and then forgets about the client, and doesn't wait for subsequent calls from the clien. 
- cacheable requests (not sure what that means)
- uniform interface - uses a uniform interface, aka URI. In our case that's a URL - https://www.javatpoint.com/uri-vs-url#:~:text=A%20URI%20is%20a%20sequence,resource%20available%20on%20the%20internet.



github has a good example of a restful api e.g. try:

```
curl -i https://api.github.com/users/sher-chowdhury
```


When designing an API, always use nouns: e.g. 

```
/getUsers         # this is bad practice
```

instead use:

```
/users
```

Also they can be plural or singular, depending on your use case. 

Also best to add in a unique identifier to the URI's path:

```
/users/sher-chowdhury
```

For security+readability reasons, it's best to ensure the unique identify isn't in the form or a primary key, because:

- We don't want to give any clues to hackers how our backend database is structured (security reason)
- You can tell from the the url, what resource we are querying for (usability reasons)

To implement this approach, we have to have a table in our database that resolved the query-string to a userid. 

Here's an example of using query strings:

```
/users?sort=name
```

You can have two types of URI paths, one with a Unique-Identifier and one without:

```
/users                 # here GET operation returns a list of resources. POST works here. 


/users/sher-chowdhury  # here GET returns one resource.  POST shouldn't work here. 
```

The GET/POST/PUT/DELETE can behave differently depending on which of the above URI's you're using. 



## Handling associations

A resource can be associated with other resources, e.g. a "customer" resource can have one or more "invoices". So if you want 
to get a list of invoices for a particular user, you can do it by sending 2 http request. 

```
api/v1/customers/sher-chowdhury

api/v1/invoices?customeromId=234

```

the first http request will return the customer resource which will cotianer the customerId value. Then we use the customeromId value as a query string to return the list of invoices.

However a better way to do this, in REST, is like this:



```
api/v1/customers/sher-chowdhury/invoices
```

This is single call, and much more efficient. 

You can have multiple associations, e.g. "payments" and "shipments" resources can be for the same customer resources. E.g. 


```
api/v1/customers/sher-chowdhury/payments
api/v1/customers/sher-chowdhury/shipments

```


## HTTP Caching.  

Caching should be implemented on the server side, and client. there's lots of tools available on do implement server side caching, e.g. redis and memcached. 

But what about client side caching?

If you do an `GET` of an object, the object returns has some version metadata. If you do another `GET` but this time provide the version info in the header, then the server checks if that version is still the latest version, and if so then it sends back an empty http-response with status code of `304 Not Modified`. This is achieved with the header stting `If-None-Match`, meaning that only return an object if the object's latest version doesn't match what we have. 

The [If-Match](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/If-Match) header setting let's you send a `PUT` (update) request, which only performs the update, if the version specified still exists on the server (assuming something else didn't update it already), if version doesn't match, then server returns a `412 Precondition Failed` HTTP response. 

### Entity Tags (ETags)
When we make a HTTP request, the HTTP response we get back usually has a `ETag` HTTP response header setting, which is usually a long string. 

```
etag: "54168b8724a40803988c5d"
```

An [Etag](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag), could indicate strong or weak caching support. if strong, then it means no need to GET request for a few weeks, (e.g. profile pic), the above etag is a strong etag. If weak, then it means the caching is only good for a short time. this time of etag looks like:

```
etag: W/"54168b8724a40803988c5d"
```






Refs:
- https://app.pluralsight.com/course-player?clipId=66a88da4-49b5-4bf6-b317-0b05a948481a