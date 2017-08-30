---
layout: post
title:  "New Blog Direction"
date:   2017-08-21 17:47:28 -0500
categories: webdev
---

This blog is about to take a sudden, and unexpected left turn.  I've been moved to a very different position in my day job, and along with this new position, have been tasked with learning as much as possible about several technologies.  In doing so, I've discovered that, though resources exist in several places for each of the technologies I'm investigating, there is very little in the way of consolidated vertical stacks of documentation.  If you want to learn how to do something in React, for example, it's relatively straightforward.  But the web isn't built on singular bits of tech.  The tough parts happen in between - how do you tie these things together?

That's what I'm going to attempt to describe.  I've reached a point with my own site where I've learned a bit about how to build things in my architecture, and believe there is value in sharing that knowledge.  So, for the forseeable future, I'm going to do my best to walk through the steps required to build a reactive website in the following tech stack:

## Current State:

- AWS Severless (server-agnostic) backend:
  - DynamoDB NoSQL database
  - S3 Cloud File hosting
  - Cloudfront
  - API Gateway endpoints
  - AWS Lambda functions to handle API requests
    - I will be using primarily Python and Node.js for these, though lambda allows for various other runtimes
- Node.js framework:
  - React web components
  - Other extension libraries (tbd!)
- Google Domains for domain management (this is less important than the rest)
- Docker for containerized services

## Stretch goals

- AWS:
  - [ ] Cognito User Access management
  - [ ] IAM permissions management
  - [ ] EC2 for services
- General Web Dev
  - [ ] Migrate this blog from GitHub pages to proprietary blogging platform

We will go through the process of registering a domain, hosting the site in AWS, setting up a dev environment using Docker and other CI/CD technologies, building the AWS backend framework for the site, and tying everything together.

The current state of the website discussed in this blog is available at [kpitzen.io](https://www.kpitzen.io/)

The source code for every piece of this website, where applicable, is available on [GitHub](https://github.com/kpitzen) 

[Next Post ->]({{ site.baseurl }}{% link _posts/2017-08-27-getting-started.markdown %})