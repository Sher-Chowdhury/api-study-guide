https://app.pluralsight.com/id?redirectTo=/library/courses/ae8db6a7-8781-4022-8fe5-3af32fa0caf0
https://jwt.io/introduction

Jwt is a security token format that are popular inprotocoals such as oauth2 and openid connect

Jwt token is a really long string of characters. This string is made up of three parts,each separated by a period.



The first and second segments starts with the characters "eyj".

The token is actually a json string that's base64 encoded.

Json web token, aka Jwt, is a standard security token format.

It lets you create a json payload that you can send to another party.


A http request is made up of 4 parts:

- path
- query string
- header 
- body

a jwt can be included to any of the above 4 sections. However "path" or "query string" are not good place to include jwts, because they are usually https/tls decrypted by the recipient server and stored in the server logs. 



Without the fear of anyone tampering with it.

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

1. User supplies creds (e.g. username/password or apikey) in initial HTTP-request to the server 
2. Server validates creds are valid, and generates jot and sends it back to the user in the form of a HTTP-response
3. User attaches token as a header setting on all subsequent http-requests until jot expires, then goes back to step one. 

jots are better than using creds, because:

1. Creds are not being sent on every single http-request (or zero times if jots being used in conjuction with Oauth)
2. jots have expiration dates, so if hacker intercepted the jot, they can't use it for long before it expires. 
3. small+compact, and cuts down the HTTP-request's body encryption+decryption overhead, when compared to just using 

This is described in this video - https://app.pluralsight.com/course-player?clipId=37efbe7f-ce27-4e6e-8437-7c34cd190ca7




