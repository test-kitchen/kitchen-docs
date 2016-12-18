---
title: Adding a Dependency
prev:
  text: "Writing a Failing Recipe"
  url: "writing-failing-recipe"
next:
  text: "Backfilling Platforms"
  url: "backfilling-platforms"
---

To add a dependency to our cookbook we edit the `metadata.rb` file and the runit cookbook with a `depends` statement:

~~~ruby
name "git"
version "0.1.0"

depends "runit", "~> 1.4.0"
~~~

Note that we're adding a constraint on the version of the runit cookbook. While this isn't strictly required it is something you want to consider as cookbooks can introduce breaking changes in the future.

Now, let's see if we get to pass our tests:

~~~
$ kitchen verify server-ubuntu-1404
-----> Starting Kitchen (v1.14.2)
-----> Converging <server-ubuntu-1404>...
       Preparing files for transfer
       Preparing dna.json
       Preparing current project directory as a cookbook
       Removing non-cookbook files before transfer
       Preparing solo.rb
-----> Chef Omnibus installation detected (install only if missing)
       Transferring files to <server-ubuntu-1404>
       Starting Chef Client, version 12.17.44
       resolving cookbooks for run list: ["git::server"]

       ================================================================================
       Error Resolving Cookbooks for Run List:
       ================================================================================

       Missing Cookbooks:
       ------------------
       No such cookbook: runit

       Expanded Run List:
       ------------------
       * git::server

       Platform:
       ---------
       x86_64-linux


       Running handlers:
       [2016-12-18T15:49:01+00:00] ERROR: Running exception handlers
       Running handlers complete
       [2016-12-18T15:49:01+00:00] ERROR: Exception handlers complete
       Chef Client failed. 0 resources updated in 01 seconds
       [2016-12-18T15:49:01+00:00] FATAL: Stacktrace dumped to /tmp/kitchen/cache/chef-stacktrace.out
       [2016-12-18T15:49:01+00:00] FATAL: Please provide the contents of the stacktrace.out file if you file a bug report
       [2016-12-18T15:49:01+00:00] ERROR: 412 "Precondition Failed"
       [2016-12-18T15:49:01+00:00] FATAL: Chef::Exceptions::ChildConvergeError: Chef run process exited unsuccessfully (exit code 1)
>>>>>> ------Exception-------
>>>>>> Class: Kitchen::ActionFailed
>>>>>> Message: 1 actions failed.
>>>>>>     Converge failed on instance <server-ubuntu-1404>.  Please see .kitchen/logs/server-ubuntu-1404.log for more details
>>>>>> ----------------------
>>>>>> Please see .kitchen/logs/kitchen.log for more details
>>>>>> Also try running `kitchen diagnose --all` for configuration
~~~

Hmm, Chef still seems to be confused. We've told Chef that we depend on the runit cookbook yet it's still complaining about our metadata.

What we need is a Chef cookbook dependency resolver tool to fetch our missing cookbooks so that Test Kitchen can use them in the Chef run. Here's the high level logic that Test Kitchen traverses in order to help you with your cookbook dependencies (again, first match wins):

1. Do you have a `Berksfile` in your project directory? If so, Test Kitchen will use [Berkshelf](http://berkshelf.com/) to resolve dependencies.
2. Do you have a `Cheffile` in your project directory? If so, Test Kitchen will use [Librarian-Chef](https://github.com/applicationsonline/librarian-chef) to resolve dependencies.
3. Do you have a `cookbooks/` directory in your project directory? If so, Test Kitchen will use that.
4. Do you have a `metadata.rb` file in your project directory? If so, then this project is a cookbook and Test Kitchen will use that.
5. Otherwise we're out of options, so Test Kitchen will create a fake cookbook and use that so that Chef has just enough to perform an empty Chef run.

For this guide, we'll use Berkshelf to fetch runit for us.

To get started we need to install the berkshelf gem--Test Kitchen doesn't depend on this library directly since it's one of many options:

~~~
$ gem install berkshelf
Fetching: public_suffix-2.0.4.gem (100%)
Successfully installed public_suffix-2.0.4
Fetching: addressable-2.5.0.gem (100%)
Successfully installed addressable-2.5.0
Fetching: multipart-post-2.0.0.gem (100%)
Successfully installed multipart-post-2.0.0
Fetching: faraday-0.9.2.gem (100%)
Successfully installed faraday-0.9.2
Fetching: httpclient-2.8.3.gem (100%)
Successfully installed httpclient-2.8.3
Fetching: hashie-3.4.6.gem (100%)
Successfully installed hashie-3.4.6
Fetching: buff-extensions-2.0.0.gem (100%)
Successfully installed buff-extensions-2.0.0
Fetching: varia_model-0.6.0.gem (100%)
Successfully installed varia_model-0.6.0
Fetching: buff-config-2.0.0.gem (100%)
Successfully installed buff-config-2.0.0
Fetching: buff-ignore-1.2.0.gem (100%)
Successfully installed buff-ignore-1.2.0
Fetching: buff-ruby_engine-1.0.0.gem (100%)
Successfully installed buff-ruby_engine-1.0.0
Fetching: buff-shell_out-1.1.0.gem (100%)
Successfully installed buff-shell_out-1.1.0
Fetching: hitimes-1.2.4.gem (100%)
Building native extensions.  This could take a while...
Successfully installed hitimes-1.2.4
Fetching: timers-4.0.4.gem (100%)
Successfully installed timers-4.0.4
Fetching: celluloid-0.16.0.gem (100%)
Successfully installed celluloid-0.16.0
Fetching: nio4r-1.2.1.gem (100%)
Building native extensions.  This could take a while...
Successfully installed nio4r-1.2.1
Fetching: celluloid-io-0.16.2.gem (100%)
Successfully installed celluloid-io-0.16.2
Fetching: mixlib-shellout-2.2.7.gem (100%)
Successfully installed mixlib-shellout-2.2.7
Fetching: mixlib-config-2.2.4.gem (100%)
Successfully installed mixlib-config-2.2.4
Fetching: fuzzyurl-0.9.0.gem (100%)
Successfully installed fuzzyurl-0.9.0
Fetching: chef-config-12.17.44.gem (100%)
Successfully installed chef-config-12.17.44
Fetching: erubis-2.7.0.gem (100%)
Successfully installed erubis-2.7.0
Fetching: mixlib-log-1.7.1.gem (100%)
Successfully installed mixlib-log-1.7.1
Fetching: mixlib-authentication-1.4.1.gem (100%)
Successfully installed mixlib-authentication-1.4.1
Fetching: retryable-2.0.4.gem (100%)
Successfully installed retryable-2.0.4
Fetching: semverse-2.0.0.gem (100%)
Successfully installed semverse-2.0.0
Fetching: ridley-5.1.0.gem (100%)
Successfully installed ridley-5.1.0
Fetching: berkshelf-api-client-3.0.0.gem (100%)
Successfully installed berkshelf-api-client-3.0.0
Fetching: cleanroom-1.0.0.gem (100%)
Successfully installed cleanroom-1.0.0
Fetching: minitar-0.5.4.gem (100%)
Successfully installed minitar-0.5.4
Fetching: molinillo-0.5.4.gem (100%)
Successfully installed molinillo-0.5.4
Fetching: solve-3.1.0.gem (100%)
Successfully installed solve-3.1.0
Fetching: thor-0.19.1.gem (100%)
Successfully installed thor-0.19.1
Fetching: sawyer-0.8.1.gem (100%)
Successfully installed sawyer-0.8.1
Fetching: octokit-4.6.2.gem (100%)
Successfully installed octokit-4.6.2
Fetching: mixlib-archive-0.2.0.gem (100%)
Successfully installed mixlib-archive-0.2.0
Fetching: berkshelf-5.3.0.gem (100%)
Successfully installed berkshelf-5.3.0
37 gems installed
~~~

Next we'll create the smallest `Berksfile`, using your editor of choice:

~~~ruby
source "https://api.berkshelf.com"

metadata
~~~

The `metadata` command tells Berkshelf that the current project is a Chef cookbook and to use its `metadata.rb` as a source of cookbook dependencies.

Now, let's re-run `kitchen verify`:

~~~
$ kitchen verify server-ubuntu-1404
-----> Starting Kitchen (v1.14.2)
-----> Converging <server-ubuntu-1404>...
       Preparing files for transfer
       Preparing dna.json
       Resolving cookbook dependencies with Berkshelf 5.3.0...
       Removing non-cookbook files before transfer
       Preparing solo.rb
-----> Chef Omnibus installation detected (install only if missing)
       Transferring files to <server-ubuntu-1404>
       Starting Chef Client, version 12.17.44
       resolving cookbooks for run list: ["git::server"]
       Synchronizing Cookbooks:
         - build-essential (7.0.2)
         - git (0.1.0)
         - runit (1.4.6)
         - seven_zip (2.0.2)
         - yum (2.4.4)
         - mingw (1.2.4)
         - compat_resource (12.16.2)
         - windows (2.1.1)
         - ohai (4.2.3)
       Installing Cookbook Gems:
       Compiling Cookbooks...
       /tmp/kitchen/cache/cookbooks/yum/providers/repository.rb:114: warning: key :type is duplicated and overwritten on line 121
       [2016-12-18T16:26:21+00:00] WARN: Chef::Provider::YumRepository already exists!  Cannot create deprecation class for LWRP provider yum_repository from cookbook yum
       [2016-12-18T16:26:21+00:00] WARN: YumRepository already exists!  Deprecation class overwrites Custom resource yum_repository from cookbook yum
       Converging 9 resources
       Recipe: git::default
         * apt_package[git] action install
           - install version 1:1.9.1-1ubuntu0.3 of package git
         * log[Well, that was too easy] action write

       Recipe: runit::default
         * service[runit] action nothing (skipped due to action :nothing)
         * execute[start-runsvdir] action nothing (skipped due to action :nothing)
         * execute[runit-hup-init] action nothing (skipped due to action :nothing)
         * apt_package[runit] action install
         Recipe: <Dynamically Defined Resource>
           * cookbook_file[/tmp/kitchen/cache/preseed/runit/runit-2.1.1-6.2ubuntu3.seed] action create
             - create new file /tmp/kitchen/cache/preseed/runit/runit-2.1.1-6.2ubuntu3.seed
             - update content in file /tmp/kitchen/cache/preseed/runit/runit-2.1.1-6.2ubuntu3.seed from none to 9c6758
             --- /tmp/kitchen/cache/preseed/runit/runit-2.1.1-6.2ubuntu3.seed	2016-12-18 16:26:29.812136180 +0000
             +++ /tmp/kitchen/cache/preseed/runit/.chef-runit-220161218-11757-1t3qb1o.1.1-6.2ubuntu3.seed	2016-12-18 16:26:29.812136180 +0000
             @@ -1 +1,2 @@
             +runit   runit/signalinit        boolean true
           - preseed package runit
           - install version 2.1.1-6.2ubuntu3 of package runit
       Recipe: runit::default
         * execute[start-runsvdir] action nothing (skipped due to action :nothing)
         * execute[runit-hup-init] action nothing (skipped due to action :nothing)
       Recipe: git::server
         * apt_package[git-daemon-run] action install
           - install version 1:1.9.1-1ubuntu0.3 of package git-daemon-run
       Recipe: <Dynamically Defined Resource>
         * service[git-daemon] action nothing (skipped due to action :nothing)
       Recipe: git::server
         * runit_service[git-daemon] action enable
         Recipe: <Dynamically Defined Resource>
           * link[/etc/init.d/git-daemon] action create
             - create symlink at /etc/init.d/git-daemon to /usr/bin/sv
           - configure service runit_service[git-daemon]

       Running handlers:
       Running handlers complete
       Chef Client finished, 7/13 resources updated in 14 seconds
       Finished converging <server-ubuntu-1404> (0m19.57s).
-----> Setting up <server-ubuntu-1404>...
       Finished setting up <server-ubuntu-1404> (0m0.00s).
-----> Verifying <server-ubuntu-1404>...
       Preparing files for transfer
-----> Installing Busser (busser)
Fetching: thor-0.19.0.gem (100%)
       Successfully installed thor-0.19.0
Fetching: busser-0.7.1.gem (100%)
       Successfully installed busser-0.7.1
       2 gems installed
       Installing Busser plugins: busser-serverspec
       Plugin serverspec installed (version 0.5.10)
-----> Running postinstall for serverspec plugin
       Suite path directory /tmp/verifier/suites does not exist, skipping.
       Transferring files to <server-ubuntu-1404>
-----> Running serverspec test suite
-----> Installing Serverspec..
Fetching: diff-lcs-1.2.5.gem (100%)
Fetching: rspec-expectations-3.5.0.gem (100%)
Fetching: rspec-mocks-3.5.0.gem (100%)
Fetching: rspec-3.5.0.gem (100%)
Fetching: rspec-its-1.2.0.gem (100%)
Fetching: multi_json-1.12.1.gem (100%)
Fetching: net-ssh-3.2.0.gem (100%)
Fetching: net-scp-1.2.1.gem (100%)
Fetching: net-telnet-0.1.1.gem (100%)
Fetching: sfl-2.3.gem (100%)
Fetching: specinfra-2.66.3.gem (100%)
Fetching: serverspec-2.37.2.gem (100%)
-----> serverspec installed (version 2.37.2)
       /opt/chef/embedded/bin/ruby -I/tmp/verifier/suites/serverspec -I/tmp/verifier/gems/gems/rspec-support-3.5.0/lib:/tmp/verifier/gems/gems/rspec-core-3.5.4/lib /opt/chef/embedded/bin/rspec --pattern /tmp/verifier/suites/serverspec/\*\*/\*_spec.rb --color --format documentation --default-path /tmp/verifier/suites/serverspec

       Git Daemon
         is listening on port 9418
         has a running service of git-daemon

       Finished in 0.09235 seconds (files took 0.24348 seconds to load)
       2 examples, 0 failures

       Finished verifying <server-ubuntu-1404> (0m15.04s).
-----> Kitchen is finished. (0m35.07s)
~~~

Awesome, and tests pass! Did you notice the `Resolving cookbook dependencies with Berkshelf 5.3.0...` line near the beginning? Confirming our success:

~~~
$ kitchen list server
Instance            Driver   Provisioner  Verifier  Transport  Last Action    Last Error
server-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        Verified       <None>
server-ubuntu-1004  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
server-centos-72    Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
~~~

Now let's backfill the other platforms.
