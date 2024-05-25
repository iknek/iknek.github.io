+++
author = "Imad Alihodzic"
title = "Tackling the Nixu Hacking CTF Challenge"
date = "2024-05-18"
description = "An account of how I solved the challenge"
tags = [
    "security",
    "pentesting",
]
categories = [
    "pentesting",
]
url = "/blog/nixu-hacking-ctf"
series = ["Pentests"]
aliases = ["pentest"]
+++

I recently stumbled across the Nixu Corporation’s CTF page, and decided to try my hand at the 'Hackin' challenge. 
Please note that whilst, yes, I am publishing this on a public blog, this page will not be indexed, and the job posting which the CTF was linked from is now gone, so the chance of me ruining the CTF is near non-existent.

<!--more-->
## First clue
This challenge begins with a login page, asking for a username and password. I decided to start by writing a random input for the username and password which, interestingly, gave me my first clue - an error exclaiming:

``` SQL
SQL error: Incorrect username / password !
```
With this information, my mind immediately turned to SQL injection, so I decide to try the classic command:
 
``` SQL
' or 1=1; --
```
With the previous command sent as the username, I was rewarded with a new page! 

{{< img src="/post/nixu-hacking-ctf/main-page.png" alt="Main page." class="my-image-class" caption="The Main Page in Question">}}


## Going Down False Paths

Obviously, I wanted to find a way deeper inside, but I wasn’t sure how to proceed, as the page seemed not to have any obvious issue when using the Inspector function in Firefox. I figured the best thing to do, thus, was collect some more information on the system. 

This consisted of an IP lookup, and a port scan courtesy of [shodan.io](https://www.shodan.io) and [criminalip.io](https://www.criminalip.io). Quickly, however, I realised that this wasn’t going to get me far due to the fact that other Nixu CTF challenges are also hosted on the same machine, on different ports. 

## A Closer Look 
With this lead going nowhere, I decided to subsequently return back to the previously found page, and make good use of my Burp Suite Pro trial. Looking at the response to my post message, in-which the username is sent as cleartext, I noticed something rather interesting about the headers: 

```` http 
HTTP/1.1 200 OK
Server: nginx
Date: Fri, 22 Mar 2024 13:23:01 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 3209
Connection: close
Vary: Cookie
Set-Cookie: session=
 eyJpZCI6MSwibG9nZ2VkaW4iOnRydWUs
 InVzZXJuYW1lIjoiYWRtaW4ifQZf2GNQ.
 Goy6u9Pc6z-JG0J6VkamSnSZnJ4; 

HttpOnly; Path=/
````

More specifically, what stuck out to me was the set-cookie header (split accross multiple lines just for formatting reasons). Namely, the cookie looked like it was a JWT token, divided into a header, payload, and signature (also tokens tend to start with ‘ey’ in base64, making them easy to spot). 
Indeed, this was confirmed when I highlighted the header and looked at the Inspector tab in Burp Suite, which automatically decoded it: 

{{< img src="/post/nixu-hacking-ctf/inspector.png" alt="Inspector field, showing decoded value." class="my-image-class" caption="Inspector Field">}}

What to do with this information, though? Again, the HTTP headers provided another clue, in the form of the ‘Vary’ field. 
After a quick web search, it turned out that it’s sent by a server to indicate which request headers the server uses for content negotiation (i.e. for deciding which version of a page to return). For example, a web page might vary the response based on the user agent, in order to serve different layouts for mobile vs. desktop users. 

## Cookie-Based Shenanigans 

In the case of the CTF page at hand, the response obviously varied on the cookie. This introduced two new questions: 
How do we change the value of the cookie the server returns to us?
What do we need to change the cookie to be? 

I decided to start by putting the entire cookie into a JWT decoder. Interestingly, the payload wasn’t a valid JSON object. Furthermore, after sending a number of post requests to the web page, I noticed that the payload and (subsequently) the signature changed for each request (though not in any consistent way such as being based on time). 

As such, I decided to focus my attention on the first part of the cookie which, as seen previously, contained the username and id of the admin user, as well as boolean for if they are logged in. 

With this in mind, I returned to my first question, and realised that the only way I could pass along  direct input to the page, and subsequently the cookie, was through the username and password fields (if not considering http request smuggling and such attacks). 
Considering the fact that the input fields were, as previously shown, susceptible to sql injection, I returned to more testing there. 

## Big Break 
After some googling, I found a useful guide with a number of tips. I thus began by fingerprinting, using the following command which selects the first username field, and joins the version version information to it: 
``` sql
' UNION SELECT '0', @@VERSION, 'c', 'd' -- -
```
This proved successful, and after checking the provided cookie, I found it to be running version 10.6.10 of MariaDB. 

Next, I decided to try and identify the database table(s). After some trial and error, this was accomplished by running the following command: 
``` sql
' UNION SELECT '1', table_name, '3', '4' 
  FROM information_schema.tables 
  WHERE table_schema = database() -- -
```
After identify the table name, it was then possible to iterate through the column names, allowing me to identify a total of 8 attributes, namely ‘id,username,password,email,USER,HOST,CURRENT_CONNECTIONS, and TOTAL_CONNECTIONS’. 

Knowing the table name, its attributes, and the fact that the admin account is simply called ‘admin’, we can then run this command to retrieve the password: 
``` sql
' UNION SELECT '0', password, '3', '4' 
  FROM accounts LIMIT 1 OFFSET 0 -- -
```
With that, I had the admin password, and using it to log in, I was greeted by the congratulatory page marking the end of the CTF!

## Final Thoughts 
Overall I found this to be a very entertaining CTF. However, I did find myself chasing down a lot of false leads, which I think could have been avoided. Thus it really reminded me of the importance of occasionally taking a step back, re-evaluating what I know, and then proceeding with a new plan.
