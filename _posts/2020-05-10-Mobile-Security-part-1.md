---
layout: post
title: Mobile Security
author: Rajat Rao
date: '2020-06-10 14:35:23 +0530'
category:
        - guides
        - tutorials
summary: Beginners guide to mobile security (Android).
thumbnail: /assets/img/posts/mobile_security.png
---

As security audit of web application are performed for multiple organization, a draw of interest can be made in Mobile applications as well.

The primary objective for a mobile application penetration test is to identify exploitable vulnerabilities in code, system, network, application, databases, Api's etc before hackers are able to discover and exploit them. Application penetration testing will reveal real-world opportunities for hackers to be able to compromise application and databases in such a way that allows for unauthorized access to sensitive data or even take-over application for malicious/non-business purposes.

Android Penetration testing can be implemented in any organization:

`1)Static:` Checks with respect to code levels will be performed

`2)Network:` Intercepting the request sent by the android application and interaction between the application and server is audited.

`3)Interactive:` While the applications provide its service, they tend to store a lot of confidential data in the respective mobile device itselfdynamically. An audit of the internal storage of the device is performed.
	
	
The Mobile Security Series will be divided into the following chapters:


--> <a href="/../chapters/Mobile-chapter-1.md">Chapter 1: Android Fundamentals (Basics)</a>

--> <a href="">Chapter 2: Static Analysis</a>

--> <a href="">Chapter 3: Dynamic Analysis</a>

--> <a href="">Chapter 4: Interactive Analysis</a>

```yml
language: ruby
cache: bundler

# Travis will build the site from gh-pages branch
# and deploy the content to master branch
# use gh-pages branch to serve for github pages
# master branch will be used for deployment

branches:
  only:
  - gh-pages
script:
  - JEKYLL_ENV=production bundle exec jekyll build --destination site

# You need to generate a Personal Access Token
# https://github.com/settings/tokens
# Add this token in environment variable GITHUB_TOKEN in Travis CI repo settings

deploy:
  provider: pages
  local-dir: ./site
  target-branch: master
  email: deploy@travis-ci.org
  name: Deployment Bot
  skip-cleanup: true
  github-token: $GITHUB_TOKEN
  keep-history: true
  on:
    branch: gh-pages

# Generate your secure token with the travis gem:
# get Github token from your Travis CI profile page
  gem install travis
  travis encrypt 'GIT_NAME="YOUR_USERNAME" GIT_EMAIL="YOUR_EMAIL" GH_TOKEN=YOUR_TOKEN' --add env.global --com

# env:
#   global:
#     secure: Example
```

All we are doing is telling Travis to pick up files from our **gh-pages** branch and push the build files to **master** branch.

##### Generate a New Github Personal Access Token

We need this token as a Environment Variable in Travis. For Travis can automatically login as you, and finish its job of building your site and pushing it to your repo's master branch.

Go to [Github Generate a New Token](https://github.com/settings/tokens) Page.

![deploy using travis](/assets/img/posts/d1.png){:class="img-fluid"}

Create a new Access Token

![deploy using travis](/assets/img/posts/d2.png){:class="img-fluid"}


##### Configure Travis

Go to [Travis](https://travis.org) and Toggle the repository access to use Travis

![deploy using travis](/assets/img/posts/d3.png){:class="img-fluid"}

Go to the repository settings page and Add Environment Variable 'GITHUB_TOKEN'
![deploy using travis](/assets/img/posts/d4.png){:class="img-fluid"}

##### Push your changes to Github

Commit your local changes in gh-pages branch

`git add .`
`git commit -m "added new post"`
`git push origin gh-pages`

After push, Travis will automatically run a build process and deploy your blog.

![deploy using travis](/assets/img/posts/d5.png){:class="img-fluid"}

You can visit your site at https://yourusername.github.io

![deploy using travis](/assets/img/posts/d6.png){:class="img-fluid"}

Done ! Enjoy your brand new devlopr-jekyll blog. You can visit the site at https://yourusername.github.io
