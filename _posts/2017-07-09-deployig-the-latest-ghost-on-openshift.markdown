---
layout: post
title: Deploying The Latest Ghost on Openshift
date: '2017-07-09'
tags:
- Blog
- Tutorial
- EN
comments: true
description: Tutorial on how to deploy the latest stable Ghost on Openshift.
categories: EN
---


![](/images/2016/12/ghost.png)

For you who want to deploy the latest [Ghost](https://tryghost.org) on [Openshift](https://openshift.redhat.com), this is the alternative Ghost Quickstart you can use as a source code. Here you go [https://github.com/norsasono/openshift-ghost-starter](https://github.com/norsasono/openshift-ghost-starter).

I seperated it from their original repository for my personal purpose. I can't promise you to always stay up-to-date, but you can make a contribution to make the repository stay alive.

# The Problem

Openshift doesn't provide you neither the recommended Node.js nor the required version to install latest stable Ghost on their server and it will cause problems if you update your recent Ghost app. The last known version you can is Ghost 0.11.7. You can read the comments from **potatoefist** on [this post](https://blog.sasono.web.id/2016/12/19/update-ghost-0-11-3-using-openshift-quickstart/).

# What did I actually do?

Luckily, even they don't provide you the Node.js version that is required to deploy the app, you can still deploy the specific Node.js version you want to use to build your apps.

I don't use their default Ghost quickstart repository, I use the specific Node.js version to run on their server first and deploy Ghost manually.

---

[https://github.com/norsasono/openshift-ghost-starter](https://github.com/norsasono/openshift-ghost-starter)

---

# Inside The Package

* Nodejs v.6.9.5 (Recomended from Ghost)
* Ghost 0.11.10 (Latest stable version)

# How To?

1. Make a new Application using [Ghost 0.7.5 Quickstart](https://openshift.redhat.com/app/console/application_type/quickstart!240)
2. Use this repository as a source.
3. Done.

Don't worry, I actually use Node v.6.9.5 as the default cartridge because the default Node.js version from [Openshift-Ghost-Quickstart](https://github.com/openshift-quickstart/openshift-ghost-quickstart) is 0.10.** and no longger suppported for the recent Ghost version.

# Is it possible to be used to deploy Ghost v 1.0.-Beta?

Possiblly yes, but I haven't tried. You know that Openshift provides you both Mysql (5.1 and 5.5) and Postgree (8.4 and 9.2), since Ghost doesn't require the higher version of mysql, it is still possible. But remember, Ghost folks have dropped the support for PostgreSQL. Be wise.

