---
layout: post
title: Blog Hosting with Firebase and Continuous Integration
categories:
  - Web Development
description: My blog is now hosted on Firebase. In this post, I explain why I moved and why you should, too.
comments: true
lang: en
---
The source files to my blog are located in a private Git repository on GitHub.
From the first day, I was unhappy with the workflow I had:

{% mermaid %}
graph LR
write[Write a post]
git[Git Commit]
build[Build the site via Jekyll]
deploy[Upload via FTP]
check[Check that nothing broke]

write --> git
git --> build
build --> deploy
deploy --> check
{% endmermaid %}

The main problem really was the FTP upload - I don't know a single convenient FTP client.

That led me research a way to omit most of these steps.
I already had heard about Continuous Integration with Travis CI and wanted to try out Firebase.
When I then saw that Travis supports Firebase deployment, I knew what to do: make it work.

I moved yesterday, and now my workflow looks like this:

{% mermaid %}
graph LR
write[Write a post]
git[Git Commit]

write --> git
{% endmermaid %}

<!--more-->

### Meet: Firebase, Travis CI

* **Firebase** is a BaaS (Backend as a Service) by Google.
  It makes high-performance Web-Apps possible by providing Storage, a Realtime-Database, and a CDN for delivery.
  It is meant both for conventional Apps and Progressive Web Apps.
  While static web hosting for blogs is not its selling point, it can be used for that as well.

* **Travis CI** is a Continous Integration-Service by a german startup located in Berlin.
  Continous Integration means that it does the whole build-chain as well as the deployment for you.
  In the context of my blog, the CI gets triggered everytime a commit happens on the Git repository.
  It then build the webpage using Jekyll, and when there are no errors in the process, it deploys the `_site` directory.

### How do you make this work?

There are three steps that need to be done:

* Setup Travis
* Setup Firebase
* Connect the two.

##### Travis CI

Configuring the Webhook for Travis is simple: Just go to travis-ci.org (in case your repo is public) / travis-ci.com (in case your repo is private), login with your GitHub account and connect the repository by flipping the corresponding switch.

![Travis CI Switch](../assets/legacy_gs_bucket/travisCI_switch.png "Travis CI Switch")

Then you need to configure Travis: Create a new file called `.travis.yml` in the root directory of your blog. Paste this content:

```yaml
language: ruby
rvm:
- 2.3.3
before_script:
- chmod +x ./build/cibuild.sh
script: ./build/cibuild.sh
sudo: false
deploy:
  skip_cleanup: true
  provider: firebase
  token:
    secure: #Token goes here
```

Lets go through this:

* `language: ruby` specifies that Travis needs to install Ruby. This is needed because Jekyll depends on Ruby.
  `rvm: - 2.3.3` specifies the version to install.
* `before_script:` contains all prerequisites that need to be met before any building is done.
  `chmod +x ./build/cibuild.sh` adds the e**x**ecutable byte to `cibuild.sh`. This is needed so that the script can be executed.
* `sudo: false` tells Travis that this build can be executed on their Containers, and that no real machine is needed for this. This is done to improve build speeds.
* `deploy:` contains the information where the built files need to be delivered.
  * `skip_cleanup: true` is essential: without that tag, Travis restores the directory to its original state - and deletes the `_site` directory.
  We don' want that.
  * `provider: firebase` is self-explaining
  * `token: secure:` contains the encrypted API key given by Firebase. I'll come back to that later.

To test if it works so far, you can remove the `deploy:` part.
Now, when you do a commit, you'll see that Travis start's a build.

##### Firebase

Go to firebase.google.com and create a new project.
Then, download the Firebase tools by running `npm install -g firebase-tools`.
Navigate to your blog directory and run `firebase init`.<br>
Select *Hosting*, your project name, and enter `_site` when you get asked for the public directory.

You can test that deploying works by running `firebase deploy`.

##### Connect the two

Once both Travis and Firebase works, you can connect the two.
For that, you need to give Travis access to your Firebase project.
Run `firebase login:ci` to generate an API key.
Basically, you now could paste this key into your `.travis.yml`, but I highly recommend encrypting your key at first.

Install the Travis tools by running `gem install travis`.
Then encrypt your key by running `travis encrypt YOUR_API_KEY`.
Replace the place holder in your `.travis.yml` with that key, and now this should work!

### Advantages of this setup

Using Continuous Integration has one big advantage: You don't need to deploy yourself.
This all get's done by Travis, which makes posting really easy.

I'm sure Firebase isn't the only hosting service that works with CI.
AWS and Heroku also are supported, and you can make every service work with Travis.

Firebase has the advantage that it is free (until a certain point in traffic) and has a great CDN.
But to be fair - moving to another hoster shouldn't be too hard.

I'm happy that this worked out :D
