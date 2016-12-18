---
title: Writing a Server Test
prev:
  text: "Add a Suite"
  url: "adding-suite"
next:
  text: "Writing a Failing Recipe"
  url: "writing-failing-recipe"
---

Now to write a test or two. Previously we've seen the bats testing framework in action but this isn't always a viable option. For example if you needed to verify that a package was installed and you needed to test that on Ubuntu and CentOS platforms, then what would you do? You need to bust out some platform detection in order to run a Debian or RPM-based command. Feels like Chef would help us here since it's good at that sort of thing. On the other hand there are advantages to treating our Chef run as a black box - a configuration-management implementation detail, if you will. So what to do?

A nice solution to the platform-agnostic test issue exists called [Serverspec](http://serverspec.org/). It is a set of RSpec matchers that can assert things about servers like packages installed, services enabled, ports listening, etc. Let's see what this looks like for our Git Daemon tests.

First we're going to create a directory for our test file:

~~~
mkdir -p test/integration/server/serverspec
~~~

Next, create a file called `test/integration/server/serverspec/git_daemon_spec.rb` with the following:

~~~ruby
require 'serverspec'

# Required by serverspec
set :backend, :exec

describe "Git Daemon" do

  it "is listening on port 9418" do
    expect(port(9418)).to be_listening
  end

  it "has a running service of git-daemon" do
    expect(service("git-daemon")).to be_running
  end

end
~~~

The beginning stanzas are RSpec and Serverspec setup and the meat of our testing is in the `describe "Git Daemon"` Ruby block. In short, it's asserting that we will have a server listening on port 9418 and a running service called "git-daemon".

As our primary target platform was Ubuntu 12.04, we'll target this one first for development. Now, in Test-Driven style we'll run `kitchen verify` to watch our tests fail spectacularly:

~~~
$ kitchen verify server-ubuntu-1404
-----> Starting Kitchen (v1.14.2)
-----> Creating <server-ubuntu-1404>...
       Bringing machine 'default' up with 'virtualbox' provider...
       ==> default: Importing base box 'bento/ubuntu-14.04'...
==> default: Matching MAC address for NAT networking...
       ==> default: Checking if box 'bento/ubuntu-14.04' is up to date...
       ==> default: Setting the name of the VM: kitchen-git-cookbook-server-ubuntu-1404_default_1482074513896_56646
       ==> default: Clearing any previously set network interfaces...
       ==> default: Preparing network interfaces based on configuration...
           default: Adapter 1: nat
       ==> default: Forwarding ports...
           default: 22 (guest) => 2222 (host) (adapter 1)
       ==> default: Running 'pre-boot' VM customizations...
       ==> default: Booting VM...
       ==> default: Waiting for machine to boot. This may take a few minutes...
           default: SSH address: 127.0.0.1:2222
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
       Vagrant instance <server-ubuntu-1404> created.
       Finished creating <server-ubuntu-1404> (0m33.15s).
-----> Converging <server-ubuntu-1404>...
       Preparing files for transfer
       Preparing dna.json
       Resolving cookbook dependencies with Berkshelf 5.3.0...
       Removing non-cookbook files before transfer
       Preparing solo.rb
-----> Installing Chef Omnibus (install only if missing)
       Downloading https://omnitruck.chef.io/install.sh to file /tmp/install.sh
       Trying wget...
       Download complete.
       ubuntu 14.04 x86_64
       Getting information for chef stable  for ubuntu...
       downloading https://omnitruck.chef.io/stable/chef/metadata?v=&p=ubuntu&pv=14.04&m=x86_64
         to file /tmp/install.sh.1483/metadata.txt
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
       Transferring files to <server-ubuntu-1404>
       Starting Chef Client, version 12.17.44
       Creating a new client identity for server-ubuntu-1404 using the validator key.
       resolving cookbooks for run list: ["git::server"]
       Synchronizing Cookbooks:
         - git (0.1.0)
       Installing Cookbook Gems:
       Compiling Cookbooks...

       ================================================================================
       Recipe Compile Error
       ================================================================================

       Chef::Exceptions::RecipeNotFound
       --------------------------------
       could not find recipe server for cookbook git

       Platform:
       ---------
       x86_64-linux


       Running handlers:
       [2016-12-18T15:22:24+00:00] ERROR: Running exception handlers
       Running handlers complete
       [2016-12-18T15:22:24+00:00] ERROR: Exception handlers complete
       Chef Client failed. 0 resources updated in 01 seconds
       [2016-12-18T15:22:24+00:00] FATAL: Stacktrace dumped to /tmp/kitchen/cache/chef-stacktrace.out
       [2016-12-18T15:22:24+00:00] FATAL: Please provide the contents of the stacktrace.out file if you file a bug report
       [2016-12-18T15:22:24+00:00] ERROR: could not find recipe server for cookbook git
       [2016-12-18T15:22:24+00:00] FATAL: Chef::Exceptions::ChildConvergeError: Chef run process exited unsuccessfully (exit code 1)
>>>>>> ------Exception-------
>>>>>> Class: Kitchen::ActionFailed
>>>>>> Message: 1 actions failed.
>>>>>>     Converge failed on instance <server-ubuntu-1404>.  Please see .kitchen/logs/server-ubuntu-1404.log for more details
>>>>>> ----------------------
>>>>>> Please see .kitchen/logs/kitchen.log for more details
>>>>>> Also try running `kitchen diagnose --all` for configuration
~~~

One quick check of `kitchen list` tells us that our instance was created but not successfully converged:

~~~
$ kitchen list server-ubuntu-1404
Instance            Driver   Provisioner  Verifier  Transport  Last Action  Last Error
server-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        Created      Kitchen::ActionFailed
~~~

Yes, you can specify one or more instances with the same Ruby regular expression globbing as any other `kitchen` subcommands.

Okay, no recipe called `server` in our Git cookbook. Let's go start one.
