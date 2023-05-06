https://app.pluralsight.com/id?redirectTo=/library/courses/ae8db6a7-8781-4022-8fe5-3af32fa0caf0
https://jwt.io/introduction

Jwt is a security token format that are popular inprotocoals such as oauth2 and openid connect

Jwt token is a really long string of characters. This string is made up of three parts,each separated by a period.



The first and second segments starts with the characters "eyj". The first and 2nd segments are actually json strings that are base64 encoded (but they are not encrypted, so anyone can view it's content be base64 decoding it)


Json web token, aka Jwt, is a standard security token format.

It lets you create a json payload that you can send to another party.


A http request is made up of 4 parts:

- path 
- query string
- header 
- body

a jwt can be included to any of the above 4 sections. However "path" or "query string" are not good places to include jwts, because they are usually https/tls decrypted by the recipient server and stored in the server logs. 



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
8. authorisation server returns an jwt back to medium.com
9. medium.com attach jwt to http requests it makes to main twitter.com 
10. twitter.com attempts to decrypt the third  segment of the jwt (signature), using the tls certificate that it has received from the authorisation certificate.
11. If successful, then it means that the token was issued by a trusted authorisation server (i.e. twitter's own authorisation server)
12. the decrypted signature contains to cheksum values of the base64 encoded strings of the first and seconnd segment's json strings. e.g. 

```
cat segment1.json | base64 | shasum -a 256
c6d34339b318c6ed020b596621c6fd4c7ae747e8ef4ca01ca36ec395c7e5c77e
```

Also see - https://osxdaily.com/2021/12/17/check-sha256-hash-mac/

13. it checks that the two checksum hashes matches with the 2 segments, and if so, then it proves that the first and second segments haven't been tampered with. once that's done it proceeds with the request. 

See example - https://jwt.io/

Note, all interactions above are occuring over https, so that the jwt doesn't get intercepted and falls into the wrong hand. 


As a minimum, the second segment (payload) must contain the following "claims":

1. user's userid
2. What permissions were granted
3. token's expiration date








jots are better than using creds, because:

1. Creds are not being sent on every single http-request (or zero times if jots being used in conjuction with Oauth)
2. jots have expiration dates, so if hacker intercepted the jot, they can't use it for long before it expires. 
3. small+compact, and cuts down the HTTP-request's body encryption+decryption overhead, when compared to just using 

This is described in this video - https://app.pluralsight.com/course-player?clipId=37efbe7f-ce27-4e6e-8437-7c34cd190ca7




