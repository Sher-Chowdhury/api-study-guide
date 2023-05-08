https://app.pluralsight.com/id?redirectTo=/library/courses/ae8db6a7-8781-4022-8fe5-3af32fa0caf0
https://jwt.io/introduction

Jwt is a security token format that are popular inprotocoals such as oauth2 and openid connect

Jwt token is a really long string of characters. This string is made up of three parts,each separated by a period. Those 3 parts are called:

1. segment 1 - Header (json string that is base64 encoded, but not encrypted)
2. segment 2 - payload (json string that is base64 encoded but not encrypted)
3. segment 3 - signature (encrypted, and only the protected resource can decrypt it due to asymmetic encryption)



The first and second segments starts with the characters "eyj". The first and 2nd segments are actually json strings that are base64 encoded (but they are not encrypted, so anyone can view it's content be base64 decoding it)


Json web token, aka Jwt, is a standard security token format.

It lets you create a json payload that you can send to another party.


A http request is made up of 4 parts:

- Request type - GET,POST,PUT,DELETE...etc. 
- path 
- query string
- header - recommended place for including jwt
- body (should not be sent in GET and DELETE requests)

a jwt can be included to any of the above 4 sections. However "path" or "query string" are not good places to include jwts, because they are usually https/tls decrypted by the recipient server and stored in the server logs. so best to store it in the http's request's header, or body.

However HTTP request's body is also not a good place, because, some HTTP Requests, namely "GET" and "DELETE" HTTP requests arent supposed to include a body section.

Hence the best place to supply the jwt, is in the HTTP request's header's section. In fact, HTTP request/response headers contains a few authentication related features that you can also take advantage of by using this, e.g. "WWW-Authenticate". which we'll cover later. 



Jwt proves a trusted party created the token.

The receipient of a jwt can check that:
- the data inside the token is valid
- that an attacker has net change the data in any way
- and that it was me who created it


The first segment of the token is called the "header" this describes the token itself. It tells you how to read and validate the token. 

second part is called the "payload". E.g. user details or requesting services. this is the actual conent of the token itself, and it's what has to be tamper proof. 

both first and second parts are not encrypted, so anyeone can vide their contents by doing a base64 decode. each key-value entry in the payload's json object is called a "claim". 

the third and final secton, is called the signature. It is again base64 encoded. It is generated using the data from header+payload along with issuer's signing key. ONLY the issuer of this token can generate and decrypt this signature. 

This part is actually a string value, rather than a json object like the first 2 segments. 

the creators of the jwt standard, says it's supposed to be pronounced as "jot". 

jwt was designed to 
- fit inside an authorisation header. 
- It's also url safe, so can be passed as part of a http query string parameter. 
- can be verified locally using an authenticated integrate check, or a digital signature.  

Here are the main steps To understand how jwt works (everything is over tls so that it eliminates man-in-the-middle attacks). 

### Oauth2 exmaple scenario

Let's say you want medium.com to auto-tweet on twitter.com on your behalf, whenever you publish a new article. For that to work, you'll want
twitter.com to trust requests coming from medium.com, but you don't want to give medium.com your twitter username+password. Instead let's assume medium.com and twitter.com supports Oauth2. So how would this work? Here we have 4 players:

1. User, with a web browser that is already logged into medium.com and twitter.com
2. twitter's authorisation server. 
3. medium.com (client applicatoin)
4. twitter.com (protected resource)

Here's the steps that takes place:

1. user go medium.com (client application) setting's page and clicks on "connect to twitter" button
2. medium.com redirects browser to user's twitter.com's authorisation server. 
3. authorisation server ask user if it wants to allow medium.com to send tweets on user's behave. 
4. user clicks on "allow"
5. Authorisatin server returns an "Authorisation Grant" back to the web browser. 
6. Web browser forwards the "Authorisation Grant" to medium.com (client application)
7. medium.com forwards "Authorisation Grant" to the authorisation server's token-request endpoint, along with some metadata. 
8. authorisation server returns an jwt back to medium.com. the signature part of this token is encrypted with the private key. 
9. medium.com attach jwt to http requests it makes to main twitter.com 
10. twitter.com checks the 1st and 2nd segments, to ensure everythign is ok, e.g. expiry time has not passed. Then it uses it's public key to decrypt the signature. If that fails then it means it's an invalid signature. 
```
cat segment1.json | base64 | shasum -a 256
c6d34339b318c6ed020b596621c6fd4c7ae747e8ef4ca01ca36ec395c7e5c77e
```

Also see - https://osxdaily.com/2021/12/17/check-sha256-hash-mac/

13. it checks that the two checksum hashes matches with the 2 segments, and if so, then it proves that the first and second segments haven't been tampered with. once that's done it proceeds with the request. 

See example - https://jwt.io/

Note, all interactions above are occuring over https, so that the jwt doesn't get intercepted and falls into the wrong hand. 










jots are better than using creds, because:

1. Creds are not being sent on every single http-request (or zero times if jots being used in conjuction with Oauth)
2. jots have expiration dates, so if hacker intercepted the jot, they can't use it for long before it expires. 
3. small+compact, and cuts down the HTTP-request's body encryption+decryption overhead, when compared to just using 

This is described in this video - https://app.pluralsight.com/course-player?clipId=37efbe7f-ce27-4e6e-8437-7c34cd190ca7





A http request's header section, is a good place to send your jwt, using the "Authorisation" setting - https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization:

```
Authorization: <authentication-scheme> <credentials>  
```

there are are few authentication schemes available, but the one to use for jwt is "bearer" - https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication#authentication_schemes

The protected resource (e.g. twitter.com) will return a HTTP response. This response is made up of 3 parts:

- status code
- header 
- content




The status code part can be "401 Unauthorised" error, if:

1. Authorization setting is missing from the http request's header section. 
2. The authentication scheme is not "bearer"
3. the supplied jwt is invalid

When this happens the HTTP response object usually includes the header claim "WWW-Authenticate" setting - https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/WWW-Authenticate which gives some on how you can successful authenticate, e.g. which authentication-scheme it can accept. It also given reason why your authentication failed, e.g. token has expired.  

The "WWW-Authenticate" can also specify a "Realm" url, which is a url for requesting a token (authorization server). 

you might also get "403 forbidden", i.e. jwt is valued, but don't have permission, i.e. authentication was successful, but authorization failed.




## Cookies

Cookies are used when user directly logs into a website using username/password. E.g. when user logs into medium.com or twitter.com. 

In this scenario the browser submits a http request to the server, which contains the username/password. The server returns a http response object that contains a cookie - https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies

The browser can then send that cookie back to the server on all subsequent http requests. 

The server returns cookies to the browser usibg the http response's header's `set-cookie` setting - https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#the_set-cookie_and_cookie_headers

And the browser sends the cookies on subsequent http-requests, using the http request's header's "cookie" setting - https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#the_set-cookie_and_cookie_headers

A common cookie key-value to set, is something like "session-id". The server side, can store sesssion-ids on it's own database to keep track of an active login session. If the server wants the user to login again, then they can simply delete the session-id from it's lookup database. However, that's not possible for jwts, instead you need to wait for the jwt to expire. Also jwt's can be used like a single use token, you can use it as many times as you want before it expires. whereas cookies can be single use. 

cookies are best suited when web-browsers are issuing http requests (since browsers have dedicated+secure storage place for storing cookie, but not jwts), whereas jwt are best suited for applications/microservices to issue http-requests. 




The 2nd segment (aka payload) of your jwt can contain a collection of key-values, this can potentially include application/permission data, but that not good practice since application+permission data may need to be changed, but that's not possible with jwts. also you can't store too much info inside this segment, otherwise you'll hit a limit, since jwts are supposed to be small and compact. So only store data in the payload segment that is unlikely to change during the jwt's lifetime. 



## Javascript Object Signing and Encryption (JOSE) standards

Ref - https://www.iana.org/assignments/jose/jose.xhtml

JOSE is a group of standards that defines jwts in more details. There are 5 main stanards that makes up JOSE:

1. [Json Web Token (JWT) - RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519): this standard covers the core token structure. 
2. [Json Web Signature (JWS) - RFC 7515](https://datatracker.ietf.org/doc/html/rfc7515): This standard covers what goes into the header section,
3. [JSON Web Encryption (JWE) - RFC 7516](https://datatracker.ietf.org/doc/html/rfc7516): JSON Web Encryption
4. [JSON Web Key (JWK) - RFC 7517](https://datatracker.ietf.org/doc/html/rfc7517): JSON Web Key (this covers shareable key formats)
5. [JSON Web Algorithms (JWA) - RFC 7518](https://datatracker.ietf.org/doc/html/rfc7518): this covers the signing+encryption algorithms.  

All the above are actually well written so definitely worth a read. 

### Json Web Token (JWT) - RFC 7519
This provides recommendations what claim nam-value pairs should be added to the payload, none of them are mandatory though - https://datatracker.ietf.org/doc/html/rfc7519#section-4.1


### Json Web Signature (JWS) - RFC 7515
This covers what goes intothe jwt's header section (the first segment). e.g.:

```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

### JSON Web Encryption (JWE) - RFC 7516
This covers an alternative jwt format, where the payload section is encrypted. This format is not that common. 


### JSON Web Key (JWK) - RFC 7517
not sure about this, but is used by Oauth and OpenID


### JSON Web Algorithms (JWA) - RFC 7518

This covers what algorithms can be used for doing encrypton. It essentially tells you what values the jwt's header's `alg` setting can have - https://datatracker.ietf.org/doc/html/rfc7518#section-3.1

There's also a dropdown list to select your algorithm on - https://jwt.io/


## Creating JWTs
The jwt's signature is created using a private key to encrypt the signature. And a public key is used to decrypt the signature. This is all possible thanks to asymmetric encryption. 

Side note: This is similar to how https/tls works on the internet. A certificate authority (verisign) has a private key (aka certificate authority), and they create a public key from that CA, which are then pre-installed into all the popular web browsers. the ca then signs certs. and those cert's contains encrypted signature, that can only be issued by verisign. 

### jwt headers

This should contain the following key-value pairs:

1. `typ` - (optional) token type, this is set as "JWT" by default. 
2. `alg` - (required) the (cheksum) algorithm used to sign the token, e.g. `echo "hello" | base64 | shasum -a 256`
3. `kid` - (optional) Key Id, what public key to use, useful if you are allowed to have multiple public keys. 

### jwt payloads
common claims are:

1. `iss` - issuer. Unique id of who created the token. It could be the name of the requesting website, e.g. medium.com, or url of the microservice (client application) 
2. `aud` - audience. the recipient of the token, who'll check that the token is valid. This is also usually a url. This can also be set as an array of values, to indicate the token can be used in a multiple of places. Note, it can be a problem to to have multiple audience, because one of the token recipient can potentially reuse the token to make calls to the other recipients. 
3. `exp` - expiry, seconds this 01/01/1970. If you don't include this then the token would never expire. 
4. `iat` - Issued at - when the token was generated
5. `sub` - subject, who the token should represent, e.g. a username, email address, an unique-id. It should be something that never chnages. 
6. `nbf` - not before. Useful if you want to make a token valid at some point in the future. 
7. `jti` - JWT ID, unique id for a token. Useful for making 1-time use tokens. It can also be used to blacklist tokens, if it has been leaked, which would effect revoke the token. Although this is not always recommended because it goes against the principle of jwt being stateless. 


none of these are mandatory, according to the official standards. However you should really use a lot of them as part of best practice as well as better security. That's why most jwt libraries will add them in by default. 

These common claims are aka as Registered Claims - https://www.iana.org/assignments/jwt/jwt.xhtml. Stick to using these as much as possible rather than creating your own custom claim types. 


### base64 vs base64url

A jwt is made up of three string segments delimited by a period. Each string is content in base64 encoded form.

Also remember you are supposed to be allowed (although not recommended) to include a jwt in a HTTP request as part of the url's path or query string. However in a url, the characters, `/`, '+' and "=" signs have special meanings. So if a base64 encode string contain any of these characters, then it would cause problems.

that's why base64url is instead of base64 for encoding in jwts. base64url is the same as base64, with the only difference beting that it replcases `+` with `-` and `/` with `_`. I think base64 encodes strings never contains any underscores, hyphens, so it's easy to convert base64url string back to base64 strings. 


base64 strings can also end with 1, 2, or 3 `=` characters. That's becuase the number of characters in a base64 string must be divisible by 4. Hence the `=` characters are used for padding at the end only, and not elsewhere within the string. base64url just strips them out as well. Check out this tool to learn more - https://www.base64url.com


### jwt signature

the encoded header string is calculated as:

```
encodedHeaderString = baseurl64(utf8($headerJson))
```

utf8 will strip out all of the json whitespacing, so that the json string is compacted into a single string. 

So we're effectively doing:

```bash
echo '{
  "typ": "JWT",
  "alg": "HS256"
}' | jq . -c | base64 | sed 's/+/-/g; s,/,_,g; s/=//g'
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9Cg
```

jq's -c flag means compact mode. 

utf8 bit not essentially, but everyone does it. 


the second segment, the payload goes through the same process. 

## Validating jwt tokens

Here's the steps a recipient takes to validate the jwt:

1. it's three strings delimited by periods
2. successfully base64url decode the first 2 segments
3. validate the json contents in the header and payloads, e.g. has the token expired? Is any mandatory values mssing, e.g. `alg` in header segment? Id `aud` is set, are you the intended audience for this token?



# 3rd party software that can be used for setting up Authorisation Servers

- [Oauth](https://auth0.com/) owned by okta. 
- [okta](https://www.okta.com/uk/)

All of these can generate jwt in slightly different formats (e.g. use different combinations of claim types), but Oauth, being the market leader created it's own more specific standard - https://datatracker.ietf.org/doc/html/rfc9068

Oauth tokens can be identified by it's header's `typ` setting, which is set to "at+jwt".

Recommended npm packages to use - https://jwt.io/libraries?language=Node.js