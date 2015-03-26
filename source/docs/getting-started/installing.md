---
title: "Installing Test Kitchen"
prev:
  text: "Introduction"
  url: ""
next:
  text: "Getting Help"
  url: "getting-help"
---

Okay let's get down to it, shall we? Much of Test Kitchen is delivered as [RubGems](http://guides.rubygems.org/what-is-a-gem/). If Ruby "stuff" is new, don't despair -- we're going to steer clear of a lot of Ruby-looking code.

To follow this guide there is some software that we will need in order for Test Kitchen to do its thing:

* A Ruby distribution - version 1.9 and higher
* ChefDK - the Chef Development Kit
* Git - oh yes, we're going to be commiting codeA
* Vagrant - helps manage development virtual machines
* VirtualBox - let's you run virtual machines locally

I know, it sounds like a lot of work but fear not. It's not overly tricky to get going, and Matt Kirkhas written an article, [Chef with Vagrant and Test Kitchen: Getting Vagrant and Tools](https://blog.safaribooksonline.com/2014/11/27/opscode-chef-vagrant-test-kitchen/), that you can follow to get started. Take a few minutes to get your software setup and if you run into any issues there should be some smart and capable people in the [#chef](http://webchat.freenode.net/?channel=chef) IRC chatroom to get you back on track.


> **All done? Great! Open a terminal window and let's confirm Test Kitchen is installed.**

To save on typing, the tool's main command is `kitchen`. So to get the currently installed version we type:

~~~
$ kitchen version
Test Kitchen version 1.3.1
~~~
