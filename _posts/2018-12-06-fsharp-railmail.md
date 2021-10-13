---
layout: post
title: "Developing RailMail (Getting to know F#)"
categories:
  - .NET
  - F#
lang: en
---

I have been experimenting with Functional Programming for quite some time now, and to strengthen my skills with F#, I developed [RailMail](https://github.com/skn0tt/RailMail), *"A Reactive Mail dispatcher written in F#"*.

In this post, I will explain the core parts of RailMail and revise what was most interesting while writing this.

![Rails](/assets/railmail/rail.svg)

<!--more-->

## What does Railmail do?

RailMail is a service that exposes a REST API for sending Email.
It was originally written as an email dispatch abstraction for [EntE](https://gitlab.com/Skn0tt/EntE), but was replaced by Nodemailer in the last month.

Why is it *Reactive* then, you might ask?
It also can listen to an AMQP queue for emails to send so you can loosely couple your services.

## Show me some code!

The following code snippet shows the main logic of the REST API.

```fsharp
let webApp: HttpHandler =
  choose [
    GET >=>
      choose [
        route "/status" >=> setStatusCode 200 >=> text "OK"
      ]
    POST >=>
      choose [
        route "/mail" >=> mailHandler
      ]
    RequestErrors.NOT_FOUND "Not Found"    
  ]
```

Giraffe (a HTTP library for F#) makes the HTTP handler look really simple because of the used DSL.  
It basically says:
Choose between `GET` and `POST`.
If it's a `GET` and goes to `/status`, set the status code to 200 and respond with "OK".
If it's a `POST` and goes to `/mail`, let `mailHandler` handle it.

The `mailHandler` returns a task, which behaves like Promises in Javascript:

```fsharp
let mailHandler (next: HttpFunc) (ctx: HttpContext) =
  task {
    let! body = ctx.ReadBodyFromRequestAsync()

    let res = processMsg body
    
    return! match res with
            | Choice1Of2 e -> RequestErrors.BAD_REQUEST e next ctx
            | Choice2Of2 _ -> setStatusCode 200 next ctx
  }
```

It first reads the body of the incoming request and then processes the it using the `processMsg` function.
Depending on `processMsg`s response, `mailHandler` returns a 200 or an error message.

`processMsg` handles the mailing logic and returns a `Choice` - a type that can either be one or the other, in this case either *error* or *success*.

```fsharp
let processMsg msg =
    let envelope = Envelope.parse msg
    match envelope with
    | Choice1Of2 error -> Choice1Of2 error
    | Choice2Of2 e -> dispatch e; Choice2Of2 ()
```

It does this by first parsing the incoming message.
If parsing was successful, so if `Envelope.parse` returns `Choice2Of2`, the mail gets dispatched and it returns another `Choice2Of2`, this time empty.
If parsing was unsuccessful, it returns a `Choice1Of`.

### JSON Parsing

```fsharp
[<CLIMutable>]
type EnvelopeBody =
  {
    text : string
    html : string
  }
  member this.HasErrors() =
    if isNull this.html && isNull this.text
      then Some "Either text or html body must be provided."
      else None

[<CLIMutable>]
type Envelope =
  {
    recipients : string list
    subject : string
    body : EnvelopeBody
  }
  member this.HasErrors() =
    this.body.HasErrors()


let parse s =
  let v = JsonConvert.DeserializeObject<Envelope> s
  
  match v.HasErrors() with
  | Some e -> Choice1Of2 e
  | None -> Choice2Of2 v
```

JSON deserialization is suprisingly easy in F#:
You just declare your types as `[<CLIMutable>]` and add some validation methods, and the you can just use the `DeserializeObject` method from the [Newtonsoft.JSON](https://www.newtonsoft.com/json) Library.

### SMTP Dispatching

```fsharp
let private config = SMTPConfig.config
let private fromAddress = new MailAddress(config.sender)

let private client = new SmtpClient(config.host, config.port)
client.EnableSsl <- true
client.Credentials <- NetworkCredential(config.username, config.password)
client.DeliveryMethod <- SmtpDeliveryMethod.Network

let private mimeType = ContentType("text/html")
```

Here you can see interaction with standard C# libraries: OOP style mutation is possible using the `<-` operator, just as Instantiation using `new`.
SMTP delivery is baked into .NET, so it is really easy.

```fsharp
let private constructMessage (e : Envelope) =
  let msg = new MailMessage()
  msg.From <- fromAddress
  msg.Body <- e.body.text
  msg.Subject <- e.subject
  
  for r in e.recipients do
    msg.To.Add(r)
  
  msg
```

`constructMessage` is used to project our JSON message on to a C# MailMessage object that can then be dispatched using the mail client:

```fsharp
let dispatch (e : Envelope) =
  use msg = constructMessage e
  client.Send msg
```

# What did I learn from this project?

Functional Programming has awesome abstractions.
The `Option` type eliminates the nasty NullException completely and enables concise, easy code where other languages would have countless `null` checks and garbled logic.

Replacing classes with data objects and letting logic reside inside of functions that act transform data object greatly simplifies testing and reasoning about code.

Immutability will save you countless headaches and creates algorithms that project data onto a transformation instead of mutating it.

# Summary

Although programming in F# is very fun, I find myself still using Typescript as my go-to language, since the Node.JS ecosystem is so huge.

But learning F# was not worthless: the three aspects I mentioned above found their way into my normal Typescript coding where I now use libraries like [Monet.js](https://monet.github.io/monet.js/) and the data mapper pattern more often.
I will explain more on functional concepts in Typescript in a future post.
