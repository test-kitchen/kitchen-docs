---
title: "Creating a Cookbook"
prev:
  text: "Getting Help"
  url: "getting-help"
next:
  text: "Writing a Recipe"
  url: "writing-recipe"
---

In order to keep our worked example as simple as possible let's create a Chef cookbook to automate the installation and management of the [Git](http://git-scm.com/) distributed version control tool. It's true that there is already a very capable [Git cookbook](http://community.opscode.com/cookbooks/git) available on the [Opscode Community Site](http://community.opscode.com/cookbooks) (not to mention we just installed Git by hand in the **Installing** section) but this lets us focus on Test Kitchen usage and workflow rather than [building more awesome](http://www.youtube.com/watch?v=OU8ihx3nT6I).

In the "real world" most Chef cookbook authors are likely to use a cookbook project generator but we're going to skip that step in the interests of transparency. A simple Chef cookbook has very few moving parts so let's start from there.

First of all, let's create an empty Git repository and enter into that directory:

~~~
$ git init git-cookbook
Initialized empty Git repository in /tmpt-cookbook/git-cookbook/.git/
$ cd git-cookbook
~~~

Note that the directory doesn't matter to Test Kitchen so feel free to call this directory `git`, `chef-git`, `chef-git-cookbook`, or `fuzzypants_git`.

Next we need a [metadata.rb](http://docs.opscode.com/config_rb_metadata.html) file so that various Chef-aware tooling can understand our cookbook. Test Kitchen is one such tool.  All it really cares about are the **name** and **version** attributes of the cookbook. Use your favorite text editor and create a file called `metadata.rb` with the following:


~~~ruby
name "git"
version "0.1.0"
~~~

Now let's setup the default recipe in this cookbook. It doesn't have to do anything for the moment but if we add it, Test Kitchen can wire this up for us without any further work.

~~~
$ mkdir recipes
$ touch recipes/default.rb
~~~

> **Congratulations. You've authored a Chef cookbook.**

Okay, it's a little light on implementation but let's commit it anyway:

~~~
$ git add metadata.rb recipes/default.rb
$ git commit -m "Git Chef cookbook - the lean version"
[master (root-commit) 087db85] Git Chef cookbook - the lean version
 1 file changed, 2 insertions(+)
 create mode 100644 metadata.rb
 create mode 100644 recipes/default.rb
~~~

Now we'll add Test Kitchen to our project by using the **init** subcommand:

~~~
$ kitchen init --driver=kitchen-vagrant
      create  .kitchen.yml
      create  chefignore
      create  .gitignore
      append  .gitignore
      append  .gitignore
Successfully installed kitchen-vagrant-0.21.1
1 gem installed
~~~

What's going on here? The `kitchen init` subcommand will create an initial configuration file for Test Kitchen called `.kitchen.yml`. We'll look at that in a moment.

A few directories were created but these are only a convenience--you don't strictly need `test/integration/default` in your project.

You can see that you have a `.gitignore` file in your project's root which will tell Git to never commit a directory called `.kitchen` and something called `.kitchen.local.yml`. Don't worry about these for now, just some housekeeping details.

Finally, a gem called `kitchen-vagrant` was installed. By itself Test Kitchen can't do very much. It needs one or more **Drivers** which are responsible for managing the virtual machines we need for testing. At present there are many different Test Kitchen Drivers but we're going to stick with the [Kitchen Vagrant Driver](https://github.com/opscode/kitchen-vagrant) for now.

|| Pro-Tip
|| The Kitchen Vagrant Driver is the default driver chosen when you omit `--driver=kitchen-vagrant` from the command. After a few projects, feel free to simply `kitchen init`.

Let's turn our attention to the `.kitchen.yml` file for a minute. While Test Kitchen may have created the initial file automatically, it's expected that you will read and edit this file. After all, you know what you want to test... right? Opening this file in your editor of choice we see something like the following:

~~~yaml
---
driver:
  name: vagrant

provisioner:
  name: chef_solo

platforms:
  - name: ubuntu-14.04
  - name: centos-7.2

suites:
  - name: default
    run_list:
      - recipe[git::default]
    attributes:
~~~

Very briefly we can cover the 4 main sections you're likely to find in a `.kitchen.yml` file:

* `driver`: This is where we configure the behaviour of the Kitchen Driver - the component that is responsible for creating a machine that we'll use to test our cookbook.  Here we set up basics like credentials, ssh usernames, sudo requirements, etc. Each Driver is responsible for requiring and using the configuration here. Under this section we have `driver.name`: This tells Test Kitchen that we want to use the `kitchen-vagrant` driver by default unless otherwise specified.
* `provisioner`: This tells Test Kitchen how to run Chef, to apply the code in our cookbook to the machine under test.  The default and simplest approach is to use `chef-solo`, but other options are available, and ultimately Test Kitchen doesn't care how the infrastructure is built - it could theoretically be with Puppet, Ansible, or Perl for all it cares.
* `platforms`: This is a list of operation systems on which we want to run our code. Note that the operating system's version, architecture, cloud environment, etc. might be relevant to what Test Kitchen considers a **Platform**.
* `suites`: This section defines what we want to test.  It includes the Chef run-list and any node attribute setups that we want run on each **Platform** above. For example, we might want to test the MySQL client cookbook code seperately from the server cookbook code for maximum isolation.

Let's say for argument's sake that we only care about running our Chef cookbook on Ubuntu 12.04 distributions. In that case, we can edit the `.kitchen.yml` file so that the list of `platforms` has only one entry like so:

~~~yaml
---
driver:
  name: vagrant

provisioner:
  name: chef_solo

platforms:
  - name: ubuntu-14.04

suites:
  - name: default
    run_list:
      - recipe[git::default]
    attributes:
~~~

To see the results of our work, let's run the `kitchen list` subcommand:

~~~
$ kitchen list
Instance             Driver   Provisioner  Verifier  Transport  Last Action    Last Error
default-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
~~~

So what's this `default-ubuntu-1404` thing and what's an "Instance"? A Test Kitchen **Instance** is a pairwise combination of a **Suite** and a **Platform** as laid out in your `.kitchen.yml` file. Test Kitchen has auto-named your only instance by combining the **Suite** name (`"default"`) and the **Platform** name (`"ubuntu-14.04"`) into a form that is safe for DNS and hostname records, namely `"default-ubuntu-1404"`.

Okay, let's spin this **Instance** up to see what happens. Test Kitchen calls this the **Create Action**. We're going to be painfully explicit and ask Test Kitchen to only create the `default-ubuntu-1404` instance:

~~~
$ kitchen create default-ubuntu-1404
-----> Starting Kitchen (v1.14.2)
-----> Creating <default-ubuntu-1404>...
       Bringing machine 'default' up with 'virtualbox' provider...
       ==> default: Importing base box 'bento/ubuntu-14.04'...
==> default: Matching MAC address for NAT networking...
       ==> default: Checking if box 'bento/ubuntu-14.04' is up to date...
       ==> default: Setting the name of the VM: kitchen-git-cookbook-default-ubuntu-1404_default_1482056833777_92056
       ==> default: Fixed port collision for 22 => 2222. Now on port 2200.
       ==> default: Clearing any previously set network interfaces...
       ==> default: Preparing network interfaces based on configuration...
           default: Adapter 1: nat
       ==> default: Forwarding ports...
           default: 22 (guest) => 2200 (host) (adapter 1)
       ==> default: Running 'pre-boot' VM customizations...
       ==> default: Booting VM...
       ==> default: Waiting for machine to boot. This may take a few minutes...
           default: SSH address: 127.0.0.1:2200
           default: SSH username: vagrant
           default: SSH auth method: private key
           default:
           default: Vagrant insecure key detected. Vagrant will automatically replace
           default: this with a newly generated keypair for better security.
           default:
           default: Inserting generated public key within guest...
           default: Removing insecure key from the guest if it's present...
           default: Key inserted! Disconnecting and reconnecting using new SSH key...
       ==> default: Machine booted and ready!
       ==> default: Checking for guest additions in VM...
       ==> default: Setting hostname...
       ==> default: Mounting shared folders...
           default: /tmp/omnibus/cache => /Users/dom/.kitchen/cache
       ==> default: Machine not provisioned because `--no-provision` is specified.
       [SSH] Established
       Vagrant instance <default-ubuntu-1404> created.
       Finished creating <default-ubuntu-1404> (0m33.04s).
-----> Kitchen is finished. (0m33.18s)
~~~

If you are a Vagrant user then the line containing `vagrant up --no-provision` will look familiar. Let's check the status of our instance now:

~~~
$ kitchen list
Instance             Driver   Provisioner  Verifier  Transport  Last Action  Last Error
default-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        Created      <None>
~~~

Let's commit our glorious work:

~~~
$ git add .gitignore .kitchen.yml chefignore
$ git commit -m "Add Test Kitchen to the project."
[master 431068c] Add Test Kitchen to the project.
 2 files changed, 17 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 .kitchen.yml
~~~

Ok, we have an instance created and ready for some Chef code. Onward!
