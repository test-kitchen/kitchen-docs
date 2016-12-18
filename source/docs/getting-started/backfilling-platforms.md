---
title: Backfilling Platforms
prev:
  text: "Adding a Dependency"
  url: "adding-dependency"
next:
  text: "Next Steps"
  url: "next-steps"
---

Let's see if our server recipe works on the older Ubuntu 10.04 platform. Fingers crossed, here we go:

~~~
$ kitchen verify server-ubuntu-1004
-----> Starting Kitchen (v1.14.2)
-----> Creating <server-ubuntu-1004>...
       Bringing machine 'default' up with 'virtualbox' provider...
       ==> default: Importing base box 'bento/ubuntu-10.04'...
==> default: Matching MAC address for NAT networking...
       ==> default: Checking if box 'bento/ubuntu-10.04' is up to date...
       ==> default: Setting the name of the VM: kitchen-git-cookbook-server-ubuntu-1004_default_1482078595260_53152
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
       Vagrant instance <server-ubuntu-1004> created.
       Finished creating <server-ubuntu-1004> (0m29.47s).
-----> Converging <server-ubuntu-1004>...
       Preparing files for transfer
       Preparing dna.json
       Resolving cookbook dependencies with Berkshelf 5.3.0...
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
         to file /tmp/install.sh.940/metadata.txt
       trying wget...
       trying perl...
       trying python...
       sha1	7d30b300f95f00036919ee8bf3b95ab73429e57e
       sha256	663d6c42c90bbb9463bc02a7c5d777f7aa6ebd52c071a0c1963bc8c4db76dea2
       url	https://packages.chef.io/files/stable/chef/12.10.24/ubuntu/10.04/chef_12.10.24-1_amd64.deb
       version	12.10.24
       downloaded metadata file looks valid...
       /tmp/omnibus/cache/chef_12.10.24-1_amd64.deb already exists, verifiying checksum...
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
       Selecting previously deselected package chef.
(Reading database ... 21183 files and directories currently installed.)
       Unpacking chef (from .../chef_12.10.24-1_amd64.deb) ...
       Setting up chef (12.10.24-1) ...
       Thank you for installing Chef!

       Transferring files to <server-ubuntu-1004>
       Starting Chef Client, version 12.10.24
       Installing Cookbook Gems:
       Compiling Cookbooks...
       [2016-12-18T16:30:30+00:00] WARN: Chef::Provider::AptRepository already exists!  Cannot create deprecation class for LWRP provider apt_repository from cookbook apt
       [2016-12-18T16:30:30+00:00] WARN: AptRepository already exists!  Deprecation class overwrites Custom resource apt_repository from cookbook apt
       Converging 17 resources
       Recipe: git::default
         * apt_package[git-core] action install
           - install version 1:1.7.0.4-1ubuntu0.2 of package git-core
         * log[Well, that was too easy] action write

       Recipe: runit::default
         * service[runit] action nothing (skipped due to action :nothing)
         * execute[start-runsvdir] action nothing (skipped due to action :nothing)
         * execute[runit-hup-init] action nothing (skipped due to action :nothing)
         * apt_package[runit] action install
         Recipe: <Dynamically Defined Resource>
           * cookbook_file[/tmp/kitchen/cache/preseed/runit/runit-2.0.0-1ubuntu4.seed] action create
             - create new file /tmp/kitchen/cache/preseed/runit/runit-2.0.0-1ubuntu4.seed
             - update content in file /tmp/kitchen/cache/preseed/runit/runit-2.0.0-1ubuntu4.seed from none to 9c6758
             --- /tmp/kitchen/cache/preseed/runit/runit-2.0.0-1ubuntu4.seed	2016-12-18 16:31:08.274826038 +0000
             +++ /tmp/kitchen/cache/preseed/runit/.chef-runit-2.0.0-1ubuntu4.seed20161218-1029-1lv87tf	2016-12-18 16:31:08.274826038 +0000
             @@ -1 +1,2 @@
             +runit   runit/signalinit        boolean true
           - preseed package runit
           - install version 2.0.0-1ubuntu4 of package runit
       Recipe: runit::default
         * execute[start-runsvdir] action nothing (skipped due to action :nothing)
         * execute[runit-hup-init] action nothing (skipped due to action :nothing)
       Recipe: git::server
         * apt_package[git-daemon-run] action install
           - install version 1:1.7.0.4-1ubuntu0.2 of package git-daemon-run
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
       Chef Client finished, 13/25 resources updated in 42 seconds
       Finished converging <server-ubuntu-1004> (0m54.92s).
-----> Setting up <server-ubuntu-1004>...
       Finished setting up <server-ubuntu-1004> (0m0.00s).
-----> Verifying <server-ubuntu-1004>...
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
       Transferring files to <server-ubuntu-1004>
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

       Finished in 0.11521 seconds (files took 0.27702 seconds to load)
       2 examples, 0 failures

       Finished verifying <server-ubuntu-1004> (0m11.62s).
-----> Kitchen is finished. (1m36.42s)
~~~

Well that was easy!

I'm going to spare us all a great deal of pain and say that getting CentOS working with Git Daemon and runit is, well, kinda nuts. Ultimately not worth it for the sake of this guide. So should we leave a Platform/Suite combination lying around that we know we won't support? Naw!

> **Add a platform name to an `excludes` array in a suite to remove the platform/suite combination from testing.**

Let's exclude the `server-centos-72` instance so that it doesn't accidentally get run. Update `.kitchen.yml` to look like the following:

~~~yaml
---
driver:
  name: vagrant

provisioner:
  name: chef_solo

platforms:
  - name: ubuntu-12.04
  - name: ubuntu-10.04
  - name: centos-7.2

suites:
  - name: default
    run_list:
      - recipe[git::default]
    attributes:
  - name: server
    run_list:
      - recipe[git::server]
    attributes:
    excludes:
      - centos-7.2
~~~

Now let's run `kitchen list` to ensure the instance is gone:

~~~
$ kitchen list
Instance             Driver   Provisioner  Verifier  Transport  Last Action    Last Error
default-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
default-ubuntu-1004  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
default-centos-72    Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
server-ubuntu-1404   Vagrant  ChefSolo     Busser    Ssh        Verified       <None>
server-ubuntu-1004   Vagrant  ChefSolo     Busser    Ssh        Verified       <None>
~~~

After so much success we should commit our working code. We want to leave our `Berksfile.lock` out of version control so first we'll edit `.gitignore` to read:

~~~
Berksfile.lock
.kitchen/
.kitchen.local.yml
~~~

Now to commit:

~~~
$ git add .gitignore .kitchen.yml metadata.rb Berksfile recipes/server.rb test/integration/server/serverspec/git_daemon_spec.rb
$ git commit -m "Add git-daemon support for Ubuntu 10.04 & 14.04."
[master 1bb6ead] Add git-daemon support for Ubuntu 10.04 & 14.04.
 6 files changed, 44 insertions(+)
 create mode 100644 Berksfile
 create mode 100644 recipes/server.rb
 create mode 100644 test/integration/server/serverspec/git_daemon_spec.rb
~~~

Finally let's destroy our running instances:

~~~
$ kitchen destroy
-----> Starting Kitchen (v1.14.2)
-----> Destroying <default-ubuntu-1404>...
       Finished destroying <default-ubuntu-1404> (0m0.00s).
-----> Destroying <default-ubuntu-1004>...
       Finished destroying <default-ubuntu-1004> (0m0.00s).
-----> Destroying <default-centos-72>...
       Finished destroying <default-centos-72> (0m0.00s).
-----> Destroying <server-ubuntu-1404>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       Vagrant instance <server-ubuntu-1404> destroyed.
       Finished destroying <server-ubuntu-1404> (0m4.60s).
-----> Destroying <server-ubuntu-1004>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       Vagrant instance <server-ubuntu-1004> destroyed.
       Finished destroying <server-ubuntu-1004> (0m3.96s).
-----> Kitchen is finished. (0m8.99s)
~~~

Okay, let's cut it off here, ship it!
