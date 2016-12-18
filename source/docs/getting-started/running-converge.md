---
title: "Running Kitchen Converge"
prev:
  text: "Writing a Recipe"
  url: "writing-recipe"
next:
  text: "Manually verifying"
  url: "manually-verifying"
---

Now that we have some code, let's let Test Kitchen run it for us on our Ubuntu 12.04 instance:

~~~
$ kitchen converge default-ubuntu-1404
-----> Starting Kitchen (v1.14.2)
-----> Converging <default-ubuntu-1404>...
       Preparing files for transfer
       Preparing dna.json
       Preparing current project directory as a cookbook
       Removing non-cookbook files before transfer
       Preparing solo.rb
-----> Installing Chef Omnibus (install only if missing)
       Downloading https://omnitruck.chef.io/install.sh to file /tmp/install.sh
       Trying wget...
       Download complete.
       ubuntu 14.04 x86_64
       Getting information for chef stable  for ubuntu...
       downloading https://omnitruck.chef.io/stable/chef/metadata?v=&p=ubuntu&pv=14.04&m=x86_64
         to file /tmp/install.sh.1569/metadata.txt
       trying wget...
       sha1	10b9026d57005aaf31289aec650931a02b5347d3
       sha256	de5991b073fb22aa295fd0142f5e4ed3ca7da6ffe2c3fdcb01da29e4cdd0bd04
       url	https://packages.chef.io/files/stable/chef/12.17.44/ubuntu/14.04/chef_12.17.44-1_amd64.deb
       version	12.17.44
       downloaded metadata file looks valid...
       /tmp/omnibus/cache/chef_12.17.44-1_amd64.deb already exists, verifiying checksum...
       Comparing checksum with sha256sum...
       checksum compare succeeded, using existing file!

       WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING

       You are installing an omnibus package without a version pin.  If you are installing
       on production servers via an automated process this is DANGEROUS and you will
       be upgraded without warning on new releases, even to new major releases.
       Letting the version float is only appropriate in desktop, test, development or
       CI/CD environments.

       WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING

       Installing chef
       installing with dpkg...
       Selecting previously unselected package chef.
(Reading database ... 35284 files and directories currently installed.)
       Preparing to unpack .../chef_12.17.44-1_amd64.deb ...
       Unpacking chef (12.17.44-1) ...
       Setting up chef (12.17.44-1) ...
       Thank you for installing Chef!
       Transferring files to <default-ubuntu-1404>
       Starting Chef Client, version 12.17.44
       Creating a new client identity for default-ubuntu-1404 using the validator key.
       resolving cookbooks for run list: ["git::default"]
       Synchronizing Cookbooks:
         - git (0.1.0)
       Installing Cookbook Gems:
       Compiling Cookbooks...
       Converging 2 resources
       Recipe: git::default
         * apt_package[git] action install
           - install version 1:1.9.1-1ubuntu0.3 of package git
         * log[Well, that was too easy] action write


       Running handlers:
       Running handlers complete
       Chef Client finished, 2/2 resources updated in 21 seconds
       Finished converging <default-ubuntu-1404> (0m28.08s).
-----> Kitchen is finished. (0m28.22s)
~~~

To quote our Chef run, that **was** too easy. If you are a Chef user then part of the output above should look familiar to you. Here's what happened at a high level:

* Chef was installed on the instance by performing an [Omnibus package installation](http://www.opscode.com/chef/install/)
* Your Git cookbook files and a minimal Chef Solo configuration were built and uploaded to the instance
* A Chef run was initiated using the run-list and node attributes specified in the `.kitchen.yml` file

There's nothing to stop you from running this command again (or over-and-over for that matter) so, let's see what happens:

~~~
$ kitchen converge default-ubuntu-1404
-----> Starting Kitchen (v1.14.2)
-----> Converging <default-ubuntu-1404>...
       Preparing files for transfer
       Preparing dna.json
       Preparing current project directory as a cookbook
       Removing non-cookbook files before transfer
       Preparing solo.rb
-----> Chef Omnibus installation detected (install only if missing)
       Transferring files to <default-ubuntu-1404>
       Starting Chef Client, version 12.17.44
       resolving cookbooks for run list: ["git::default"]
       Synchronizing Cookbooks:
         - git (0.1.0)
       Installing Cookbook Gems:
       Compiling Cookbooks...
       Converging 2 resources
       Recipe: git::default
         * apt_package[git] action install (up to date)
         * log[Well, that was too easy] action write


       Running handlers:
       Running handlers complete
       Chef Client finished, 1/2 resources updated in 01 seconds
       Finished converging <default-ubuntu-1404> (0m2.73s).
-----> Kitchen is finished. (0m2.86s)
~~~

That ran a **lot** faster didn't it? Here's what happened this time:

* Test Kitchen found that Chef was present and installed so skipped a re-installation.
* The same Chef cookbook files and Chef Solo configuration was uploaded to the instance. Test Kitchen is optimizing for **freshness of code and configuration over speed**. Although we all like speed wherever possible.
* A Chef run is initiated and runs very quickly as we are in the desired state.

|| Pro-Tip
|| A lot of time and effort has gone into ensuring that the exit code of Test Kitchen is always appropriate. Here is the Kitchen Command Guarantee:
|| 1. I will always exit with code **0** if my operation was successful.
|| 2. I will always exit with a non-zero code if **any** part of my operation was not successful.
|| 3. Any behavior to the contrary **is a bug**.
|| This exit code behavior is a fundamental prerequisite for any tool working in a Continuous Integration (CI) environment.

Let's check the status of our instance:

~~~
$ kitchen list
Instance             Driver   Provisioner  Verifier  Transport  Last Action  Last Error
default-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        Converged    <None>
~~~

A clean converge run, success! Let's commit our production code and move on:

~~~
$ git add recipes/default.rb
$ git commit -m "Implemented the default recipe, woot woot."
[master 4d54a16] Implemented the default recipe, woot woot.
 1 file changed, 3 insertions(+)
~~~
