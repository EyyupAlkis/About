---
layout: post
title: Fire-and-forget HTTP Requests using Node.js
description: If you don't care about the response of your HTTP request, you can use the technique in this tutorial to save some network bandwidth.
categories:
  - Web Development
  - Javascript
  - Network
  - Quirrel
lang: en
---

In most cases, we care about the results of our HTTP Requests. 
But sometimes, it's just about *making* the request, and not so much about the response.
In these cases, it may be wise to save yourself some network bandwidth by ignoring the response entirely.

<!--more-->

> I'm building a task queueing solution for Next.js! Check out [Quirrel](https://quirrel.dev?ref=nextnestarticle), I think you'll like it ☺️

# How does this work?

HTTP Requests are based on TCP connections.
Running `curl google.com` will open up a TCP connection to `google.com:80` and send roughly the following:

```http
GET / HTTP/1.1
Host: google.com
User-Agent: curl/7.64.1
Accept: */*

```

| Line | Meaning                                                      |
| ---- | ------------------------------------------------------------ |
| 1    | Request Line. Contains Method, Location and Protocol Version |
| 2    | The only *required* header: "Host"                           |
| 3, 4 | Some Headers                                                 |
| 5    | blank line to separate header and body                       |

Normally, you'll then wait for the server to respond like so:

```http
HTTP/1.1 200 
Content-Type: text/html

<html><head> ...
```

But the response isn't of interest to us.
We'll just send off our request, then terminate the TCP connection
and carry on with more interesting stuff.

# A rough test implementation

The first thing we need is a test server we can make our calls against:

```js
const http = require("http");

http
  .createServer((req, res) => {
    req.on("end", () => {
      setTimeout(
        () => {
          console.log("Done");
          res.statusCode = 200;
          res.end("Done");
        },
        1000
      );
    });
  })
  .listen(5000);
```

To simulate time-consuming work, this server delays responding by one second.

Now onto the interesting part: Making the manual HTTP request.

```js
const net = require("net");

const client = new net.Socket();

client.connect(5000, "localhost", () => {
  // request line
  client.write("POST / HTTP/1.1\r\n");

  // headers
  client.write("Host: localhost:5000\r\n");
  client.write(`Content-Length: 11\r\n`);

  // blank line
  client.write("\r\n");

  // body
  client.write("Hello World");

  // end the connection prematurely
  client.destroy();
});

client.on("close", () => {
  console.log("Connection Closed");
});
```

It's rather simple!
We open up the connection to `localhost:5000` and dispatch the request.
The important line is the call to `client.destroy()`, which will end the socket immediately after the request has been transmitted.

Let's see it in action:

![Fire and Forget in Action](/assets/fire-and-forget/fire-and-forget.gif)

# Conclusion

Fire-and-forget HTTP calls aren't that hard to make.
They can be of use in bandwith-constrained environments - I will probably make use of them for [Quirrel](https://github.com/quirrel.dev).

If this post was useful or interesting to you, make sure leave a comment or write me a message on Twitter! I always love to hear about my content helping others.
