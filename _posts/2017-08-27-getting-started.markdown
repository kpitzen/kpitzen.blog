---
layout: post
title:  "Baby Steps"
date:   2017-08-27 10:02:28 -0500
categories: webdev
---

Today, we're going to talk about hosting a static website on AWS.

This post will assume basic knowledge of:
  - Static website construction (HTML, CSS)

If you're just starting out, and aren't familiar with these concepts, there are good tutorials here:
  - [HTML](https://www.w3schools.com/html/)
  - [CSS](https://www.w3schools.com/css/)

The only other requirement for this post is an AWS account.  You can start with a free-tier account [here](https://aws.amazon.com/free/).  Once that's created, be sure to go [here](https://aws.amazon.com/cli/) and install the AWS CLI.

So you've decided to do web development, and you've, for some reason, decided you wanted to use a server-agnostic architecture for your backend. Specifically, Amazon Web Services.  For my site, I started with the basics.  I built a static site using nothing but HTML, CSS, and a few free stock images pulled from Google.  I won't cover the details here, but let's assume your site looks something liks this:

```
.
+-- assets
|   +-- image1.jpg
|   +-- image2.jpg
+-- index.html
+-- index.css
```

The fastest way to get a site like this up and running is to use AWS CloudFront.  It's possible to handle everything by just using your domain provider's DNS settings to point to a bucket in S3, but you don't get any of the tools CloudFront provides when you do things that way (as I did for a few months).  Things like HTTP -> HTTPS forwarding, content invalidation, and custom SSL certificates.  These are nice to have if you're going to expose either your data or user data, so I'd recommend it.

To get started, walk through the steps in AWS' documentation [here](http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/GettingStarted.html).  Once that's done, you should have all of your static site files stored in an AWS S3 bucket, and being hosted at whichever URL CloudFront assigned to you.  From there, you just need to add a CNAME record to your domain provider pointing to that URL, and your static site should be ready.  There are clean ways to update that content, but for now, you can use the AWS CLI installed at the beginning of this post to run:

    aws s3 cp <files> s3://<bucketname>

Next time, we'll get into how to get your static site set up in React, and what the React-AWS workflow looks like.

[<- Previous Post]({{ site.baseurl }}{% link _posts/2017-08-21-new-blog-direction.markdown %})