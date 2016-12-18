---
title: Fixing Converge
prev:
  text: "Dynamic Configuration"
  url: "dynamic-configuration"
next:
  text: "Add a New Feature"
  url: "adding-feature"
---

News of the amazing Git cookbook starts to spread to all corners of your workplace. Now a colleague has expressed interest in running the cookbook on a fleet of older Ubuntu 10.04 systems.  You mention that 2009 called, and it wants its iPhone 3 and Three Wolf Moon t-shirt back, but they just look at you blankly.  You muse over whether your not inconsiderable talents are wasted in this job, and then press on.  No problem, they should be good to go, right? Just to be sure let's add explicit testing for this platform.

Open `.kitchen.yml` in your editor and add a `ubuntu-10.04` entry to the platforms list:

~~~yaml
---
driver:
  name: vagrant

provisioner:
  name: chef_solo

platforms:
  - name: ubuntu-14.04
  - name: ubuntu-10.04
  - name: centos-7.2

suites:
  - name: default
    run_list:
      - recipe[git::default]
    attributes:
~~~

And run `kitchen list` to confirm the introduction of our latest instance:

~~~
$ kitchen list
Instance             Driver   Provisioner  Verifier  Transport  Last Action    Last Error
default-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
default-ubuntu-1004  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
default-centos-72    Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
~~~

Now we'll run the **test** subcommand and go grab a coffee:

~~~
$ kitchen test 10
-----> Starting Kitchen (v1.14.2)
-----> Cleaning up any prior instances of <default-ubuntu-1004>
-----> Destroying <default-ubuntu-1004>...
       Finished destroying <default-ubuntu-1004> (0m0.00s).
-----> Testing <default-ubuntu-1004>
-----> Creating <default-ubuntu-1004>...
       Bringing machine 'default' up with 'virtualbox' provider...
       ==> default: Box 'bento/ubuntu-10.04' could not be found. Attempting to find and install...
           default: Box Provider: virtualbox
           default: Box Version: >= 0
       ==> default: Loading metadata for box 'bento/ubuntu-10.04'
           default: URL: https://atlas.hashicorp.com/bento/ubuntu-10.04
       ==> default: Adding box 'bento/ubuntu-10.04' (v2.2.3) for provider: virtualbox
           default: Downloading: https://atlas.hashicorp.com/bento/boxes/ubuntu-10.04/versions/2.2.3/providers/virtualbox.box
==> default: Successfully added box 'bento/ubuntu-10.04' (v2.2.3) for 'virtualbox'!
       ==> default: Importing base box 'bento/ubuntu-10.04'...
==> default: Matching MAC address for NAT networking...
       ==> default: Checking if box 'bento/ubuntu-10.04' is up to date...
       ==> default: Setting the name of the VM: kitchen-git-cookbook-default-ubuntu-1004_default_1482064389709_39381
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
           default: The guest additions on this VM do not match the installed version of
           default: VirtualBox! In most cases this is fine, but in rare cases it can
           default: prevent things such as shared folders from working properly. If you see
           default: shared folder errors, please make sure the guest additions within the
           default: virtual machine match the version of VirtualBox you have installed on
           default: your host and reload your VM.
           default:
           default: Guest Additions Version: 5.0.12
           default: VirtualBox Version: 5.1
       ==> default: Setting hostname...
       ==> default: Mounting shared folders...
           default: /tmp/omnibus/cache => /Users/dom/.kitchen/cache
       ==> default: Machine not provisioned because `--no-provision` is specified.
       [SSH] Established
       Vagrant instance <default-ubuntu-1004> created.
       Finished creating <default-ubuntu-1004> (11m52.36s).
-----> Converging <default-ubuntu-1004>...
       Preparing files for transfer
       Preparing dna.json
       Preparing current project directory as a cookbook
       Removing non-cookbook files before transfer
       Preparing solo.rb
-----> Installing Chef Omnibus (install only if missing)
       Downloading https://omnitruck.chef.io/install.sh to file /tmp/install.sh
       Trying wget...
       Trying python...
       Download complete.
       ubuntu 10.04 x86_64
       Getting information for chef stable  for ubuntu...
       downloading https://omnitruck.chef.io/stable/chef/metadata?v=&p=ubuntu&pv=10.04&m=x86_64
         to file /tmp/install.sh.905/metadata.txt
       trying wget...
       trying perl...
       trying python...
       sha1	7d30b300f95f00036919ee8bf3b95ab73429e57e
       sha256	663d6c42c90bbb9463bc02a7c5d777f7aa6ebd52c071a0c1963bc8c4db76dea2
       url	https://packages.chef.io/files/stable/chef/12.10.24/ubuntu/10.04/chef_12.10.24-1_amd64.deb
       version	12.10.24
       downloaded metadata file looks valid...
       downloading https://packages.chef.io/files/stable/chef/12.10.24/ubuntu/10.04/chef_12.10.24-1_amd64.deb
         to file /tmp/omnibus/cache/chef_12.10.24-1_amd64.deb
       trying wget...
       trying perl...
       trying python...
       Comparing checksum with sha256sum...

       WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING

       You are installing an omnibus package without a version pin.  If you are installing
       on production servers via an automated process this is DANGEROUS and you will
       be upgraded without warning on new releases, even to new major releases.
       Letting the version float is only appropriate in desktop, test, development or
       CI/CD environments.

       WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING WARNING

       Installing chef
       installing with dpkg...
       Selecting previously deselected package chef.
(Reading database ... 21183 files and directories currently installed.)
       Unpacking chef (from .../chef_12.10.24-1_amd64.deb) ...
       Setting up chef (12.10.24-1) ...
       Thank you for installing Chef!

       Transferring files to <default-ubuntu-1004>
       Starting Chef Client, version 12.10.24
       Installing Cookbook Gems:
       Compiling Cookbooks...
       Converging 2 resources
       Recipe: git::default
         * apt_package[git] action install

           ================================================================================
           Error executing action `install` on resource 'apt_package[git]'
           ================================================================================

           Chef::Exceptions::Package
           -------------------------
           git has no candidate in the apt-cache

           Resource Declaration:
           ---------------------
           # In /tmp/kitchen/cookbooks/git/recipes/default.rb

             1: package "git"
             2:

           Compiled Resource:
           ------------------
           # Declared in /tmp/kitchen/cookbooks/git/recipes/default.rb:1:in `from_file'

           apt_package("git") do
             package_name "git"
             action [:install]
             retries 0
             retry_delay 2
             default_guard_interpreter :default
             declared_type :package
             cookbook_name :git
             recipe_name "default"
           end

           Platform:
           ---------
           x86_64-linux


       Running handlers:
       [2016-12-18T12:35:46+00:00] ERROR: Running exception handlers
       Running handlers complete
       [2016-12-18T12:35:46+00:00] ERROR: Exception handlers complete
       Chef Client failed. 0 resources updated in 02 seconds
       [2016-12-18T12:35:46+00:00] FATAL: Stacktrace dumped to /tmp/kitchen/cache/chef-stacktrace.out
       [2016-12-18T12:35:46+00:00] FATAL: Please provide the contents of the stacktrace.out file if you file a bug report
       [2016-12-18T12:35:46+00:00] ERROR: apt_package[git] (git::default line 1) had an error: Chef::Exceptions::Package: git has no candidate in the apt-cache
       [2016-12-18T12:35:46+00:00] FATAL: Chef::Exceptions::ChildConvergeError: Chef run process exited unsuccessfully (exit code 1)
>>>>>> ------Exception-------
>>>>>> Class: Kitchen::ActionFailed
>>>>>> Message: 1 actions failed.
>>>>>>     Converge failed on instance <default-ubuntu-1004>.  Please see .kitchen/logs/default-ubuntu-1004.log for more details
>>>>>> ----------------------
>>>>>> Please see .kitchen/logs/kitchen.log for more details
>>>>>> Also try running `kitchen diagnose --all` for configuration
~~~

Oh noes! Argh, why!? Let's login to the instance and see if we can figure out what the correct package is:

~~~
$ kitchen login 10
Linux default-ubuntu-1004 2.6.32-38-server #83-Ubuntu SMP Wed Jan 4 11:26:59 UTC 2012 x86_64 GNU/Linux
Ubuntu 10.04.4 LTS

Welcome to the Ubuntu Server!
 * Documentation:  http://www.ubuntu.com/server/doc
New release 'precise' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Sat Nov 30 22:22:24 2013 from 10.0.2.2
vagrant@default-ubuntu-1004:~$ sudo apt-cache search git | grep ^git
git-buildpackage - Suite to help with Debian packages in Git repositories
git-cola - highly caffeinated git gui
git-load-dirs - Import upstream archives into git
gitg - git repository viewer for gtk+/GNOME
gitmagic - guide about Git version control system
gitosis - git repository hosting application
gitpkg - tools for maintaining Debian packages with git
gitstats - statistics generator for git repositories
git-core - fast, scalable, distributed revision control system
git-doc - fast, scalable, distributed revision control system (documentation)
gitk - fast, scalable, distributed revision control system (revision tree visualizer)
git-arch - fast, scalable, distributed revision control system (arch interoperability)
git-cvs - fast, scalable, distributed revision control system (cvs interoperability)
git-daemon-run - fast, scalable, distributed revision control system (git-daemon service)
git-email - fast, scalable, distributed revision control system (email add-on)
git-gui - fast, scalable, distributed revision control system (GUI)
git-svn - fast, scalable, distributed revision control system (svn interoperability)
gitweb - fast, scalable, distributed revision control system (web interface)
vagrant@default-ubuntu-1004:~$ exit
logout
Connection to 127.0.0.1 closed.
~~~

Okay, it looks like we want to install the `git-core` package for this release of Ubuntu. Let's fix this up back in the default recipe. Open up `recipes/default.rb` and edit to something like:

~~~ruby
if node['platform'] == "ubuntu" && node['platform_version'].to_f <= 10.04
  package "git-core"
else
  package "git"
end

log "Well, that was too easy"
~~~

This may not be pretty but let's verify that it works first on Ubuntu 10.04:

~~~
$ kitchen verify 10
-----> Starting Kitchen (v1.14.2)
-----> Creating <default-ubuntu-1004>...
       Bringing machine 'default' up with 'virtualbox' provider...
       ==> default: Checking if box 'bento/ubuntu-10.04' is up to date...
       ==> default: Machine not provisioned because `--no-provision` is specified.
       [SSH] Established
       Vagrant instance <default-ubuntu-1004> created.
       Finished creating <default-ubuntu-1004> (0m5.77s).
-----> Converging <default-ubuntu-1004>...
       Preparing files for transfer
       Preparing dna.json
       Resolving cookbook dependencies with Berkshelf 5.3.0...
       Removing non-cookbook files before transfer
       Preparing solo.rb
-----> Chef Omnibus installation detected (install only if missing)
       Transferring files to <default-ubuntu-1004>
       Starting Chef Client, version 12.10.24
       Installing Cookbook Gems:
       Compiling Cookbooks...
       [2016-12-18T14:39:06+00:00] WARN: Chef::Provider::AptRepository already exists!  Cannot create deprecation class for LWRP provider apt_repository from cookbook apt
       [2016-12-18T14:39:06+00:00] WARN: AptRepository already exists!  Deprecation class overwrites Custom resource apt_repository from cookbook apt
       Converging 2 resources
       Recipe: git::default
         * apt_package[git-core] action install
           - install version 1:1.7.0.4-1ubuntu0.2 of package git-core
         * log[Well, that was too easy] action write


       Running handlers:
       Running handlers complete
       Chef Client finished, 2/2 resources updated in 02 seconds
       Finished converging <default-ubuntu-1004> (0m3.27s).
-----> Setting up <default-ubuntu-1004>...
       Finished setting up <default-ubuntu-1004> (0m0.00s).
-----> Verifying <default-ubuntu-1004>...
       Preparing files for transfer
-----> Busser installation detected (busser)
       Installing Busser plugins: busser-bats
       Plugin bats already installed
       Removing /tmp/verifier/suites/bats
       Transferring files to <default-ubuntu-1004>
-----> Running bats test suite
 ✓ git binary is found in PATH

       1 test, 0 failures
       Finished verifying <default-ubuntu-1004> (0m3.26s).
-----> Kitchen is finished. (0m12.74s)
~~~

Back to green, good. Let's verify that the other two instances are still good. We'll use a Ruby regular expression to glob the two other instances into one Test Kitchen command:

~~~
$ kitchen verify '(14|72)'
-----> Starting Kitchen (v1.14.2)
-----> Creating <default-ubuntu-1404>...
       Bringing machine 'default' up with 'virtualbox' provider...
       ==> default: Importing base box 'bento/ubuntu-14.04'...
==> default: Matching MAC address for NAT networking...
       ==> default: Checking if box 'bento/ubuntu-14.04' is up to date...
       ==> default: Setting the name of the VM: kitchen-git-cookbook-default-ubuntu-1404_default_1482072025256_65340
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
       Finished creating <default-ubuntu-1404> (0m33.22s).
-----> Converging <default-ubuntu-1404>...
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
         - ubuntu (2.0.0)
         - apt (5.0.0)
         - compat_resource (12.16.2)
       Installing Cookbook Gems:
       Compiling Cookbooks...
       [2016-12-18T14:40:56+00:00] WARN: Chef::Provider::AptRepository already exists!  Cannot create deprecation class for LWRP provider apt_repository from cookbook apt
       [2016-12-18T14:40:56+00:00] WARN: AptRepository already exists!  Deprecation class overwrites Custom resource apt_repository from cookbook apt
       Converging 2 resources
       Recipe: git::default
         * apt_package[git] action install
           - install version 1:1.9.1-1ubuntu0.3 of package git
         * log[Well, that was too easy] action write


       Running handlers:
       Running handlers complete
       Chef Client finished, 2/2 resources updated in 14 seconds
       Finished converging <default-ubuntu-1404> (0m23.04s).
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
       Finished verifying <default-ubuntu-1404> (0m5.32s).
-----> Creating <default-centos-72>...
       Bringing machine 'default' up with 'virtualbox' provider...
       ==> default: Importing base box 'bento/centos-7.2'...
==> default: Matching MAC address for NAT networking...
       ==> default: Checking if box 'bento/centos-7.2' is up to date...
       ==> default: Setting the name of the VM: kitchen-git-cookbook-default-centos-72_default_1482072084722_73400
       ==> default: Fixed port collision for 22 => 2222. Now on port 2201.
       ==> default: Clearing any previously set network interfaces...
       ==> default: Preparing network interfaces based on configuration...
           default: Adapter 1: nat
       ==> default: Forwarding ports...
           default: 22 (guest) => 2201 (host) (adapter 1)
       ==> default: Running 'pre-boot' VM customizations...
       ==> default: Booting VM...
       ==> default: Waiting for machine to boot. This may take a few minutes...
           default: SSH address: 127.0.0.1:2201
           default: SSH username: vagrant
           default: SSH auth method: private key
           default: Warning: Remote connection disconnect. Retrying...
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
       /etc/profile.d/lang.sh: line 19: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
       [SSH] Established
       Vagrant instance <default-centos-72> created.
       Finished creating <default-centos-72> (0m51.74s).
-----> Converging <default-centos-72>...
       Preparing files for transfer
       Preparing dna.json
       Resolving cookbook dependencies with Berkshelf 5.3.0...
       Removing non-cookbook files before transfer
       Preparing solo.rb
       /etc/profile.d/lang.sh: line 19: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
-----> Installing Chef Omnibus (install only if missing)
       Downloading https://omnitruck.chef.io/install.sh to file /tmp/install.sh
       Trying wget...
       Download complete.
       el 7 x86_64
       Getting information for chef stable  for el...
       downloading https://omnitruck.chef.io/stable/chef/metadata?v=&p=el&pv=7&m=x86_64
         to file /tmp/install.sh.11078/metadata.txt
       trying wget...
       sha1	ffa2c60116d57c8b987ee2e906bff9106c98daf5
       sha256	beccc11f5861bde369f62ca3fd7361b536786e91d73f538857e625a622f8caef
       url	https://packages.chef.io/files/stable/chef/12.17.44/el/7/chef-12.17.44-1.el7.x86_64.rpm
       version	12.17.44
       downloaded metadata file looks valid...
       /tmp/omnibus/cache/chef-12.17.44-1.el7.x86_64.rpm already exists, verifiying checksum...
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
       installing with rpm...
       warning: /tmp/omnibus/cache/chef-12.17.44-1.el7.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 83ef826a: NOKEY
       Preparing...                          ################################# [100%]
       Updating / installing...
          1:chef-12.17.44-1.el7              ################################# [100%]
       Thank you for installing Chef!
       /etc/profile.d/lang.sh: line 19: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
       Transferring files to <default-centos-72>
       /etc/profile.d/lang.sh: line 19: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
       Starting Chef Client, version 12.17.44
       Creating a new client identity for default-centos-72 using the validator key.
       resolving cookbooks for run list: ["git::default"]
       Synchronizing Cookbooks:
         - git (0.1.0)
         - ubuntu (2.0.0)
         - compat_resource (12.16.2)
         - apt (5.0.0)
       Installing Cookbook Gems:
       Compiling Cookbooks...
       [2016-12-18T14:42:14+00:00] WARN: Chef::Provider::AptRepository already exists!  Cannot create deprecation class for LWRP provider apt_repository from cookbook apt
       [2016-12-18T14:42:14+00:00] WARN: AptRepository already exists!  Deprecation class overwrites Custom resource apt_repository from cookbook apt
       Converging 2 resources
       Recipe: git::default
         * yum_package[git] action install
           - install version 1.8.3.1-6.el7_2.1 of package git
         * log[Well, that was too easy] action write


       Running handlers:
       Running handlers complete
       Chef Client finished, 2/2 resources updated in 36 seconds
       Finished converging <default-centos-72> (0m44.16s).
-----> Setting up <default-centos-72>...
       Finished setting up <default-centos-72> (0m0.00s).
-----> Verifying <default-centos-72>...
       Preparing files for transfer
       /etc/profile.d/lang.sh: line 19: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
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
       /etc/profile.d/lang.sh: line 19: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
       Suite path directory /tmp/verifier/suites does not exist, skipping.
       Transferring files to <default-centos-72>
       /etc/profile.d/lang.sh: line 19: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
-----> Running bats test suite
 ✓ git binary is found in PATH

       1 test, 0 failures
       Finished verifying <default-centos-72> (0m4.85s).
-----> Kitchen is finished. (2m42.79s)
$ echo $?
0
~~~

We've successfully verified all three instances, so let's shut them down.

~~~
$ kitchen destroy
-----> Starting Kitchen (v1.14.2)
-----> Destroying <default-ubuntu-1404>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       Vagrant instance <default-ubuntu-1404> destroyed.
       Finished destroying <default-ubuntu-1404> (0m4.92s).
-----> Destroying <default-ubuntu-1004>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       Vagrant instance <default-ubuntu-1004> destroyed.
       Finished destroying <default-ubuntu-1004> (0m4.01s).
-----> Destroying <default-centos-72>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       Vagrant instance <default-centos-72> destroyed.
       Finished destroying <default-centos-72> (0m4.17s).
-----> Kitchen is finished. (0m13.57s)
~~~

And finally commit our code updates:

~~~
$ git add .kitchen.yml recipes/default.rb
$ git commit -m "Add support for Ubuntu 10.04."
[master 0c49ced] Add support for Ubuntu 10.04.
 2 files changed, 6 insertions(+), 1 deletion(-)
~~~
