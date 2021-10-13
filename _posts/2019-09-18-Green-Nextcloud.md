---
layout: post
title: "Selfhosted Nextcloud with regional redundancy"
categories:
  - "Self-Hosting"
  - "Web Development"
lang: en
---

In this post, I will explain how to setup a low-maintenance, carbon-neutral selfhosted Nextcloud instance that stores its data in Google Cloud Storage.
To make the setup easier, we'll be using Docker.

![GCS Nextcloud Tree](/assets/green_nextcloud/tree.png)

<!--more-->

For the last few years, all my important data was backed up in Google Drive.
I chose it because I was already deep into the Google ecosystem, the first ten gigabytes of storage are free and it's just easy to use.

Then, something weird started happening: The more I got in touch with Software Development, the more I started to become interested in free and open source software - I just like the idea of services that you *can* selfhost.
It gives me peace of mind that I'll still be able to access my data when the vendor goes out of business.

I can hear you screaming: "Geez, Google will rather have eaten the world before it goes out of business. Why care about it?"
Yes, you're right. Google won't go out of business anytime soon, but still: They could become evil (some argue they already are) or they could just change their pricing model.
Which would also be kind of evil.

[![Google Evil](/assets/green_nextcloud/google_evil.png)](https://medium.com/@stewofkc/google-removes-dont-be-evil-from-its-code-of-conduct-c636bf18be1c)

So, in order to stay out of Google's walled gardens (beautiful, but still walled), I started to think about how I could switch to a selfhost-able data and backups cloud.
My requirements:

1. **Low Maintenance.** If it were high maintenance, I would've surely fucked something up and all of my data would've been gone at some point.
2. **Regional redundancy.** I don't want to lose all my data due to some data center burning down or having a water damage.
3. **Cross-Platform Support.** I want to access my data on my phone, on all desktop systems and in the browser.

## Introducing: Nextcloud

As it turns out, requirement #3 limits the range to pretty much just Nextcloud - which is great, since it's widely supported and there even are dedicated Nextcloud hosting companies.
I tried out [Hetzner's Nextcloud hosting](https://www.hetzner.com/cloud), which had two big flaws: It's not regionally redundant and you cannot assign a custom domain to it.
Since all Nextcloud hosters I found out about were either expensive or didn't feature regional redundancy, I decided to look into self-hosting.

Since Low-Maintenance was one of my requirements, I did some research to find out how to abstract away as much responsibility as possible and assign them to some cloud provider.
This is how I found about [using object storage as primary storage backends](https://docs.nextcloud.com/server/17/admin_manual/configuration_files/primary_storage.html).
You can imagine object storage as some API that models a file system and allows you to store arbitrary BLOBs of data in it.
The beauty of it is that the specifics of storage, retention and backups are abstracted away by the provider and you don't have to worry about it.
Well-known object storage solutions are [Amazon S3](https://aws.amazon.com/de/s3/), [Google's GCS](https://cloud.google.com/storage/) or [Azure Storage](https://azure.microsoft.com/de-de/services/storage/), which are cloud-hosted solutions; you could also use OpenStacks's [Swift](https://github.com/openstack/swift) or [Minio](https://min.io), which are both open source and can be self-hosted.

[![Papa cloud: What do you want to be when you grow up - A Cumulonimbus? Baby cloud: Data storage.](/assets/green_nextcloud/cloud_joke.png)](https://community.spiceworks.com/topic/2164125-tell-us-your-best-cloud-tech-jokes-win-halloween-lego-minifigures?page=1)

Using S3 was the most obvious since it's natively supported by Nextcloud.
Nonetheless, I decided against using it since Amazon's AWS draws its power from coal plants, which I don't want to support.
Both Microsoft's Azure and Google's GCP draw their power from renewable energies and compensate their carbon emissions by donating to e.g. rainforest projects, so in the end I decided to use GCP.

Remember: It's not about being free from Google but being free from Google's products. Object storage is pretty similar, and I could easily move my data from GCP to some other host the future while still using my Nextcloud instance.

There's one problem, though: Nextcloud does not have built-in support for GCP's protocol, just for the S3 protocol (Maybe thats a PR to be made? 😃).
For translating between these two, I used Minio, which has a S3-Compliant interface and can use GCS for its own storage backend.
You can think of it as a mediator between the two protocols.

In the following chapter, I will explain how to setup all of this on a tiny VM.

## Setting up Nextcloud with Minio

To follow this guide, you'll need a VM with Docker and Docker-Compose installed ([How to install Docker on Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/), [How to install Docker-Compose](https://docs.docker.com/compose/install/)).
I personally use and recommend Hetzner's cloud offering, which is cheap and has servers located in the EU.
For me, the smallest size (1 core, 2gb RAM) definitely sufficed, but your mileage may vary.

To begin, rent a VM and SSH into it. Place [this `docker-compose.yml`](https://gist.github.com/Skn0tt/b731810f10922b4a3f6e6a2a9998bd91) on your server.
We will fill out the blanks in a moment.

[![Docker Joke](/assets/green_nextcloud/containers_meme.png)](https://xkcd.com/1988/)

If you don't know about Docker yet, take a moment to [read about it](https://docs.docker.com/get-started/).
Docker-Compose is a tool that's used to easily specify deployment details of multiple Docker containers.
If you're interested in how the file is structured, have a look [at the documentation](https://docs.docker.com/compose/compose-file/).
I will try to explain the different services, and their interdependencies using the follwowing diagram:

{% mermaid %}
graph TD
  Internet((Internet))
  Traefik
  Postgres
  Nextcloud
  pgbackup[Postgres Backup]
  Redis
  Minio
  GCS

  Internet --makes HTTPS requests--> Traefik
  Traefik --"proxies to (as HTTP traffic)"--> Nextcloud
  Nextcloud --stores files in--> Minio
  Nextcloud --stores user data in--> Postgres
  Nextcloud --uses as cache--> Redis
  Minio --uses as storage backend--> GCS
  Postgres --> pgbackup
  pgbackup --stores backups in--> Minio
{% endmermaid %}

> Note that this diagram models data flow and service dependency, not control flow.

The `docker-compose.yml` file you copied earlier basically encodes this model and your deployment configuration.

Before deploying this, we'll need make sure everything is configured, so let's go through all required settings and fill in the placeholders in `docker-compose.yml`:

First, think of the administrator credentials that should be initially set for your user.
Enter them in the fields `NEXTCLOUD_ADMIN_USER` and `NEXTCLOUD_ADMIN_PASSWORD`.
Replace the placehoder for `NEXTCLOUD_TRUSTED_DOMAINS` with the domain you're deploying your server to.
If you don't have a domain name that you want to use, you can also fill in the IP address of the server, so Nextcloud knows which requests to respond to (if you're using an IP address, have a look at the arguments of the `traefik` service to make sure it doesn't try to apply for SSL certificates).
You'll also need to enter your domain/IP in one of the `nextcloud` serivces labels, the corresponding place is marked as `put_in_your_domain`.
This label will tell Traefik (the reverse proxy that handles HTTPS certificates and SSL resolution in our setup) to route incoming requests on this domain to the Nextcloud service.

The next thing to configure is the Minio instance.
Remember: Since Minio has a S3-Compliant interface, it handles translation between S3 and GCS.
Think of a good pair of credentials (you don't need to remember them, just use some random generator) and enter thim into `MINIO_ACCESS_KEY` and `MINIO_SECRET_KEY`.
To connect Postgres Backup, fill in the access- and secret key you just created into `S3_ACCESS_KEY_ID` and `S3_SECRET_ACCESS_KEY`.
Think of a good name for the bucket your backups should be stored (it has to be globally unique amongst all of GCS's buckets) and enter it into `S3_BUCKET`.

To connect Nextcloud to Minio, create the file [`s3.config.php`](https://gist.github.com/Skn0tt/b731810f10922b4a3f6e6a2a9998bd91) in the same directory as your `docker-compose.yml`.
It will be mounted into the config directory of the Nextcloud container so it's taken into account on Nextcloud's startup.
Fill out the `key` and `secret` fields using your access- and secret keys.
Also fill in another unique bucket name into the `bucket` field.

The last configuration step needed is to hook up Minio with GCS (if you decided to use some other storage provider, have a look at the Minio documentation).
To begin, go to [console.cloud.google.com](https://console.cloud.google.com) and create a GCP account if you don't already have one.

Once you have access to your account, go on to create the storage buckets.
Search for "Storage", click on the corresponding result item and then on "Create New Bucket".
Give it a name, select "Multi-Region" as the location type and leave the remaining options as they are.
Click "Create" to finish setup.

Now you just need to obtain credentials for Minio to access GCS.
Search for "Service accounts", click on the corresponding result, click on "Create Service Account" and fill in some service account name and description.
Press on "Continue" and select "Storage Object Admin" as its role.
Create a key of type `JSON` and download it to your computer.
This file contains the key that's needed to authenticate with GCS.
Move it to your virtual machine and place it in the same directory as your `docker-compose.yml`.
It needs to be named `credentials.json`.

Now that everything is configured properly, you can create the deployment.
Run `docker-compose up -d`.
You'll now see how you're containers are starting.
Run `docker-compose logs -f` to see logs while they are starting.
Once everything's settled you can close the logs using `CTRL-C`.

Open your browser and go to the domain or IP address of your server.
You'll now see the Nextcloud login screen, where you can use the admin credentials you set earlier.

This concludes the setup!
Have fun with your selfhosted, regionally redundant Nextcloud instance.


## Conclusion

I'm running this Nextcloud setup for some months now.
The cost is slightly higher than Google Drive, but you get the benefits of an open-source solution and prevent vendor lock-in.
I love my instance, because it's reliable and works flawlessly with all my devices.
It also feels great to be in control your own infrastructure 😄

Hopefully, this post inspired you to host your own Nextcloud instance using object storage as its backend.
Tweet at me ([@skn0tt](https://twitter.com/skn0tt)) or let me know in the comments how it worked out for you!
I'd love to hear your thoughts about this post, too.
