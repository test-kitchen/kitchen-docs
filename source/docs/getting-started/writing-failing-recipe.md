---
title: Writing a Failing Recipe
prev:
  text: "Writing a Server Test"
  url: "writing-server-test"
next:
  text: "Adding a Dependency"
  url: "adding-dependency"
---

So far our cookbook has no dependencies on other cookbooks to get its job done which is awesome! But let's say we require dependant cookbooks. For our example we'll be relying on the [runit](http://community.opscode.com/cookbooks/runit) cookbook to manage our Git Daemon service.


With this solution in mind we'll create a file called `recipes/server.rb` with the following:

~~~ruby
include_recipe "git"
include_recipe "runit"

package "git-daemon-run"

runit_service "git-daemon" do
  sv_templates false
end
~~~

Reasonably straight forward code. Here's what is going on:

* We include our default Git recipe so that Git is installed. Hey we already solved that one, right?
* We include the default recipe from the runit cookbook which installs runit and provides the `runit_service` resource.
* We install the `git-daemon-run` package which gives us the `git-daemon` program, on Ubuntu 12.04 at least.
* Finally, we declare a runit service called `git-daemon` without generating the run and log scripts (they were provided by the Ubuntu package).

Now to see the fruits of our effort:

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
       Synchronizing Cookbooks:
         - git (0.1.0)
       Installing Cookbook Gems:
       Compiling Cookbooks...
       [2016-12-18T15:48:29+00:00] WARN: MissingCookbookDependency:
       Recipe `runit` is not in the run_list, and cookbook 'runit'
       is not a dependency of any cookbook in the run_list.  To load this recipe,
       first add a dependency on cookbook 'runit' in the cookbook you're
       including it from in that cookbook's metadata.


       ================================================================================
       Recipe Compile Error in /tmp/kitchen/cache/cookbooks/git/recipes/server.rb
       ================================================================================

       Chef::Exceptions::CookbookNotFound
       ----------------------------------
       Cookbook runit not found. If you're loading runit from another cookbook, make sure you configure the dependency in your metadata

       Cookbook Trace:
       ---------------
         /tmp/kitchen/cache/cookbooks/git/recipes/server.rb:2:in `from_file'

       Relevant File Content:
       ----------------------
       /tmp/kitchen/cache/cookbooks/git/recipes/server.rb:

         1:  include_recipe "git"
         2>> include_recipe "runit"
         3:
         4:  package "git-daemon-run"
         5:
         6:  runit_service "git-daemon" do
         7:    sv_templates false
         8:  end
         9:

       Platform:
       ---------
       x86_64-linux


       Running handlers:
       [2016-12-18T15:48:29+00:00] ERROR: Running exception handlers
       Running handlers complete
       [2016-12-18T15:48:29+00:00] ERROR: Exception handlers complete
       Chef Client failed. 0 resources updated in 01 seconds
       [2016-12-18T15:48:29+00:00] FATAL: Stacktrace dumped to /tmp/kitchen/cache/chef-stacktrace.out
       [2016-12-18T15:48:29+00:00] FATAL: Please provide the contents of the stacktrace.out file if you file a bug report
       [2016-12-18T15:48:29+00:00] ERROR: Cookbook runit not found. If you're loading runit from another cookbook, make sure you configure the dependency in your metadata
       [2016-12-18T15:48:29+00:00] FATAL: Chef::Exceptions::ChildConvergeError: Chef run process exited unsuccessfully (exit code 1)
>>>>>> ------Exception-------
>>>>>> Class: Kitchen::ActionFailed
>>>>>> Message: 1 actions failed.
>>>>>>     Converge failed on instance <server-ubuntu-1404>.  Please see .kitchen/logs/server-ubuntu-1404.log for more details
>>>>>> ----------------------
>>>>>> Please see .kitchen/logs/kitchen.log for more details
>>>>>> Also try running `kitchen diagnose --all` for configuration
~~~

See what the Chef run told you?

>> "Cookbook runit not found. If you're loading runit from another cookbook, make sure you configure the dependency in your metadata"

Hey, that's exactly what we need to do!
