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
$ kitchen converge default-ubuntu-1204
-----> Starting Kitchen (v1.3.1)
-----> Converging <default-ubuntu-1204>...
       Preparing files for transfer
       Preparing dna.json
       Preparing current project directory as a cookbook
       Removing non-cookbook files before transfer
       Preparing solo.rb
-----> Installing Chef Omnibus (install only if missing)
       downloading https://www.chef.io/chef/install.sh
         to file /tmp/install.sh
       trying wget...
       Downloading Chef  for ubuntu...
       downloading https://www.chef.io/chef/metadata?v=&prerelease=false&nightlies=false&p=ubuntu&pv=12.04&m=x86_64
         to file /tmp/install.sh.4317/metadata.txt
       
       url	https://opscode-omnibus-packages.s3.amazonaws.com/ubuntu/12.04/x86_64/chef_12.2.0-1_amd64.deb
       md5	5a900fd8f1dbcc97982f440f19fe530b
       sha256	2f6e75e7b91558c6cbb26040c440cbfcaf2e2e989c29680cee5821667f31f294
       downloaded metadata file looks valid...
       downloading https://opscode-omnibus-packages.s3.amazonaws.com/ubuntu/12.04/x86_64/chef_12.2.0-1_amd64.deb
         to file /tmp/install.sh.4317/chef_12.2.0-1_amd64.deb
       trying wget...
       Comparing checksum with sha256sum...
       Installing Chef 
       installing with dpkg...
       Selecting previously unselected package chef.
(Reading database ... 56035 files and directories currently installed.)
       Unpacking chef (from .../chef_12.2.0-1_amd64.deb) ...
       Setting up chef (12.2.0-1) ...
       Thank you for installing Chef!
       Transferring files to <default-ubuntu-1204>
       Starting Chef Client, version 12.2.0
       Compiling Cookbooks...
       Converging 2 resources
       Recipe: git::default
         * apt_package[git] action install
           - install version 1:1.7.9.5-1 of package git
         * log[Well, that was too easy] action write
         
       
       Running handlers:
       Running handlers complete
       Chef Client finished, 2/2 resources updated in 11.464101832 seconds
       Finished converging <default-ubuntu-1204> (0m38.11s).
-----> Kitchen is finished. (0m38.68s)
~~~

To quote our Chef run, that **was** too easy. If you are a Chef user then part of the output above should look familiar to you. Here's what happened at a high level:

* Chef was installed on the instance by performing an [Omnibus package installation](http://www.opscode.com/chef/install/)
* Your Git cookbook files and a minimal Chef Solo configuration were built and uploaded to the instance
* A Chef run was initiated using the run-list and node attributes specified in the `.kitchen.yml` file

There's nothing to stop you from running this command again (or over-and-over for that matter) so, let's see what happens:

~~~
$ kitchen converge default-ubuntu-1204
-----> Starting Kitchen (v1.3.1)
-----> Converging <default-ubuntu-1204>...
       Preparing files for transfer
       Preparing dna.json
       Preparing current project directory as a cookbook
       Removing non-cookbook files before transfer
       Preparing solo.rb
-----> Chef Omnibus installation detected (install only if missing)
       Transferring files to <default-ubuntu-1204>
       Starting Chef Client, version 12.2.0
       Compiling Cookbooks...
       Converging 2 resources
       Recipe: git::default
         * apt_package[git] action install (up to date)
         * log[Well, that was too easy] action write
         
       
       Running handlers:
       Running handlers complete
       Chef Client finished, 1/2 resources updated in 1.794280256 seconds
       Finished converging <default-ubuntu-1204> (0m3.61s).
-----> Kitchen is finished. (0m4.21s)
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
Instance             Driver   Provisioner  Last Action
default-ubuntu-1204  Vagrant  ChefSolo     Converged
~~~

A clean converge run, success! Let's commit our production code and move on:

~~~
$ git add recipes/default.rb
$ git commit -m "Implemented the default recipe, woot woot."
[master 4d54a16] Implemented the default recipe, woot woot.
 1 file changed, 3 insertions(+)
~~~
