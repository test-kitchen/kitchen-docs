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
$ kitchen test default-ubuntu-1204
-----> Starting Kitchen (v1.3.1)
-----> Verifying <default-ubuntu-1204>...
       Removing /tmp/busser/suites/bats
       Uploading /tmp/busser/suites/bats/git_installed.bats (mode=0644)
-----> Running bats test suite
 ✓ git binary is found in PATH
       
       1 test, 0 failures
       Finished verifying <default-ubuntu-1204> (0m1.73s).
-----> Kitchen is finished. (0m2.31s)
oyd:my-kitchen ll$ kitchen test default-ubuntu-1204
-----> Starting Kitchen (v1.3.1)
-----> Cleaning up any prior instances of <default-ubuntu-1204>
-----> Destroying <default-ubuntu-1204>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       ==> default: Removing hosts
       Vagrant instance <default-ubuntu-1204> destroyed.
       Finished destroying <default-ubuntu-1204> (0m7.25s).
-----> Testing <default-ubuntu-1204>
-----> Creating <default-ubuntu-1204>...
       Bringing machine 'default' up with 'virtualbox' provider...
       ==> default: Importing base box 'opscode-ubuntu-12.04'...
==> default: Matching MAC address for NAT networking...
       ==> default: Setting the name of the VM: kitchen-my-kitchen-default-ubuntu-1204_default_1427434052143_80960
       ==> default: Clearing any previously set network interfaces...
       ==> default: Preparing network interfaces based on configuration...
           default: Adapter 1: nat
       ==> default: Forwarding ports...
           default: 22 => 2222 (adapter 1)
       ==> default: Booting VM...
       ==> default: Waiting for machine to boot. This may take a few minutes...    default: SSH address: 127.0.0.1:2222
           default: SSH username: vagrant
           default: SSH auth method: private key
           default: Warning: Connection timeout. Retrying...
           default: 
           default: Vagrant insecure key detected. Vagrant will automatically replace
           default: this with a newly generated keypair for better security.
           default: 
           default: Inserting generated public key within guest...
           default: Removing insecure key from the guest if its present...
           default: Key inserted! Disconnecting and reconnecting using new SSH key...
       ==> default: Machine booted and ready!
       GuestAdditions versions on your host (4.3.26) and guest (4.3.8) do not match.
       stdin: is not a tty
       Reading package lists...Building dependency tree...Reading state information...dkms is already the newest version.
       linux-headers-3.11.0-15-generic is already the newest version.
       0 upgraded, 0 newly installed, 0 to remove and 2 not upgraded.
       Copy iso file /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso into the box /tmp/VBoxGuestAdditions.iso
       stdin: is not a tty
       mount: block device /tmp/VBoxGuestAdditions.iso is write-protected, mounting read-only
       Installing Virtualbox Guest Additions 4.3.26 - guest version is 4.3.8
       stdin: is not a tty
       Verifying archive integrity... All good.
       Uncompressing VirtualBox 4.3.26 Guest Additions for Linux............VirtualBox Guest Additions installer
       Removing installed version 4.3.8 of VirtualBox Guest Additions...
       Copying additional installer modules ...
       Installing additional modules ...
       Removing existing VirtualBox DKMS kernel modules ...done.
       Removing existing VirtualBox non-DKMS kernel modules ...done.
       Building the VirtualBox Guest Additions kernel modules ...done.
        ...done.
       You should restart your guest to make sure the new modules are actually used
       Installing the Window System driversCould not find the X.Org or XFree86 Window System, skipping.
       An error occurred during installation of VirtualBox Guest Additions 4.3.26. Some functionality may not work as intended.
       In most cases it is OK that the "Window System drivers" installation failed.
       stdin: is not a tty
       ==> default: Checking for guest additions in VM...
       ==> default: Checking for host entries
       ==> default: Setting hostname...
       ==> default: Machine not provisioning because `--no-provision` is specified.
       Vagrant instance <default-ubuntu-1204> created.
       Finished creating <default-ubuntu-1204> (2m43.39s).
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
         to file /tmp/install.sh.4306/metadata.txt
       trying wget...
       url	https://opscode-omnibus-packages.s3.amazonaws.com/ubuntu/12.04/x86_64/chef_12.2.0-1_amd64.deb
       md5	5a900fd8f1dbcc97982f440f19fe530b
       sha256	2f6e75e7b91558c6cbb26040c440cbfcaf2e2e989c29680cee5821667f31f294
       downloaded metadata file looks valid...
       downloading https://opscode-omnibus-packages.s3.amazonaws.com/ubuntu/12.04/x86_64/chef_12.2.0-1_amd64.deb
         to file /tmp/install.sh.4306/chef_12.2.0-1_amd64.deb
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
       Chef Client finished, 2/2 resources updated in 11.324956023 seconds
       Finished converging <default-ubuntu-1204> (0m36.21s).
-----> Setting up <default-ubuntu-1204>...
Fetching: thor-0.19.0.gem (100%)
       Successfully installed thor-0.19.0
Fetching: busser-0.7.0.gem (100%)
       Successfully installed busser-0.7.0
       2 gems installed
-----> Setting up Busser
       Creating BUSSER_ROOT in /tmp/busser
       Creating busser binstub
       Plugin bats installed (version 0.3.0)
-----> Running postinstall for bats plugin
       Installed Bats to /tmp/busser/vendor/bats/bin/bats
       Finished setting up <default-ubuntu-1204> (0m9.49s).
-----> Verifying <default-ubuntu-1204>...
       Suite path directory /tmp/busser/suites does not exist, skipping.
       Uploading /tmp/busser/suites/bats/git_installed.bats (mode=0644)
-----> Running bats test suite
 ✓ git binary is found in PATH
       
       1 test, 0 failures
       Finished verifying <default-ubuntu-1204> (0m1.78s).
-----> Destroying <default-ubuntu-1204>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       ==> default: Removing hosts
       Vagrant instance <default-ubuntu-1204> destroyed.
       Finished destroying <default-ubuntu-1204> (0m8.57s).
       Finished testing <default-ubuntu-1204> (3m46.73s).
-----> Kitchen is finished. (3m47.22s)
~~~

There's only one remaining action left that needs a mention: the **Destroy Action** which... destroys the instance. With this in mind, here's what Test Kitchen is doing in the **Test Action**:

1. Destroys the instance if it exists (`Cleaning up any prior instances of <default-ubuntu-1204>`)
2. Creates the instance (`Creating <default-ubuntu-1204>`)
3. Converges the instance (`Converging <default-ubuntu-1204>`)
4. Sets up Busser and runner plugins on the instance (`Setting up <default-ubuntu-1204>`)
5. Verifies the instance by running Busser tests (`Verifying <default-ubuntu-1204>`)
6. Destroys the instance (`Destroying <default-ubuntu-1204>`)

A few details with regards to test:

* Test Kitchen will abort the run on the instance at the first sign of trouble. This means that if your Chef run fails then Busser won't be installed and the instance won't be destroyed. This gives you a chance to inspect the state of the instance and fix any issues.
* The behavior of the final destroy action can be overridden if desired. Check out the documentation for the `--destroy` flag using `kitchen help test`.
* The primary use case in mind for this meta-action is in a Continuous Integration environment or a command for developers to run before check in or after a fresh clone. If you're using this in your test-code-verify development cycle it's going to quickly become very slow and frustrating. You're better off running the **converge** and **verify** subcommands in development and save the **test** subcommand when you need to verify the end-to-end run of your code.

Finally, let's check the status of the instance:

~~~
$ kitchen list
Instance             Driver   Provisioner  Last Action
default-ubuntu-1204  Vagrant  ChefSolo     <Not Created>
~~~

Back to square one.
