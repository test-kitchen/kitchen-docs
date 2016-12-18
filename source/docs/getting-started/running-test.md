---
title: Running Kitchen Test
prev:
  text: "Running Kitchen Verify"
  url: "running-verify"
next:
  text: "Adding a Platform"
  url: "adding-platform"
---

Now it's time to introduce to the **test** meta-action which helps you automate all the previous actions so far into one command. Recall that we currently have our instance in a "verified" state. With this in mind, let's run `kitchen test`:

~~~
$ kitchen test default-ubuntu-1404
-----> Starting Kitchen (v1.14.2)
-----> Cleaning up any prior instances of <default-ubuntu-1404>
-----> Destroying <default-ubuntu-1404>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       Vagrant instance <default-ubuntu-1404> destroyed.
       Finished destroying <default-ubuntu-1404> (0m4.71s).
-----> Testing <default-ubuntu-1404>
-----> Creating <default-ubuntu-1404>...
       Bringing machine 'default' up with 'virtualbox' provider...
       ==> default: Importing base box 'bento/ubuntu-14.04'...
==> default: Matching MAC address for NAT networking...
       ==> default: Checking if box 'bento/ubuntu-14.04' is up to date...
       ==> default: Setting the name of the VM: kitchen-git-cookbook-default-ubuntu-1404_default_1482059149281_96132
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
       Vagrant instance <default-ubuntu-1404> created.
       Finished creating <default-ubuntu-1404> (0m33.01s).
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
         to file /tmp/install.sh.1482/metadata.txt
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
       Chef Client finished, 2/2 resources updated in 14 seconds
       Finished converging <default-ubuntu-1404> (0m22.94s).
-----> Setting up <default-ubuntu-1404>...
       Finished setting up <default-ubuntu-1404> (0m0.00s).
-----> Verifying <default-ubuntu-1404>...
       Preparing files for transfer
-----> Installing Busser (busser)
Fetching: thor-0.19.0.gem (100%)
       Successfully installed thor-0.19.0
Fetching: busser-0.7.1.gem (100%)
       Successfully installed busser-0.7.1
       2 gems installed
       Installing Busser plugins: busser-bats
       Plugin bats installed (version 0.3.0)
-----> Running postinstall for bats plugin
       Installed Bats to /tmp/verifier/vendor/bats/bin/bats
       Suite path directory /tmp/verifier/suites does not exist, skipping.
       Transferring files to <default-ubuntu-1404>
-----> Running bats test suite
 ✓ git binary is found in PATH

       1 test, 0 failures
       Finished verifying <default-ubuntu-1404> (0m5.49s).
-----> Destroying <default-ubuntu-1404>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       Vagrant instance <default-ubuntu-1404> destroyed.
       Finished destroying <default-ubuntu-1404> (0m4.15s).
       Finished testing <default-ubuntu-1404> (1m10.31s).
-----> Kitchen is finished. (1m10.44s)
~~~

There's only one remaining action left that needs a mention: the **Destroy Action** which... destroys the instance. With this in mind, here's what Test Kitchen is doing in the **Test Action**:

1. Destroys the instance if it exists (`Cleaning up any prior instances of <default-ubuntu-1404>`)
2. Creates the instance (`Creating <default-ubuntu-1404>`)
3. Converges the instance (`Converging <default-ubuntu-1404>`)
4. Sets up Busser and runner plugins on the instance (`Setting up <default-ubuntu-1404>`)
5. Verifies the instance by running Busser tests (`Verifying <default-ubuntu-1404>`)
6. Destroys the instance (`Destroying <default-ubuntu-1404>`)

A few details with regards to test:

* Test Kitchen will abort the run on the instance at the first sign of trouble. This means that if your Chef run fails then Busser won't be installed and the instance won't be destroyed. This gives you a chance to inspect the state of the instance and fix any issues.
* The behavior of the final destroy action can be overridden if desired. Check out the documentation for the `--destroy` flag using `kitchen help test`.
* The primary use case in mind for this meta-action is in a Continuous Integration environment or a command for developers to run before check in or after a fresh clone. If you're using this in your test-code-verify development cycle it's going to quickly become very slow and frustrating. You're better off running the **converge** and **verify** subcommands in development and save the **test** subcommand when you need to verify the end-to-end run of your code.

Finally, let's check the status of the instance:

~~~
$ kitchen list
Instance             Driver   Provisioner  Verifier  Transport  Last Action    Last Error
default-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
~~~

Back to square one.
