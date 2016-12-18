---
title: Adding a Platform
prev:
  text: "Running Kitchen Test"
  url: "running-test"
next:
  text: "Dynamic Configuration"
  url: "dynamic-configuration"
---

Now that we are masters of the Ubuntu platform, let's add support for CentOS to our cookbook. This shouldn't be too bad. Open `.kitchen.yml` in your editor and the `centos-7.2` line to your platforms list so that it resembles:

~~~yaml
---
driver:
  name: vagrant

provisioner:
  name: chef_solo

platforms:
  - name: ubuntu-12.04
  - name: centos-7.2

suites:
  - name: default
    run_list:
      - recipe[git::default]
    attributes:
~~~

Now let's check the status of our instances:

~~~
$ kitchen list
Instance             Driver   Provisioner  Verifier  Transport  Last Action    Last Error
default-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
default-centos-72    Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
~~~

We're going to use two shortcuts here in the next command:

* Each Test Kitchen instance has a simple state machine that tracks where it is in its lifecyle. Given its current state and the desired state, the instance is smart enough to run all actions in between current and desired. In our next example we will take an instance from not created to verified in one command.
* Any `kitchen` subcommand that takes an instance name as an argument can take a Ruby regular expression that will be used to glob a list of instances together. This means that `kitchen test ubuntu` would run the **test** action only the ubuntu instance. Note that the **list** subcommand also takes the regex-globbing argument so feel free to experiment there. In our next example we'll select the `default-centos-72` instance with simply `72`.

Let's see how CentOS runs our cookbook:

~~~
$ kitchen destroy
-----> Starting Kitchen (v1.14.2)
-----> Destroying <default-ubuntu-1404>...
       Finished destroying <default-ubuntu-1404> (0m0.00s).
-----> Destroying <default-centos-72>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       Vagrant instance <default-centos-72> destroyed.
       Finished destroying <default-centos-72> (0m6.46s).
-----> Kitchen is finished. (0m6.74s)
thoth:git-cookbook dom$ kitchen verify 72
-----> Starting Kitchen (v1.14.2)
-----> Creating <default-centos-72>...
       Bringing machine 'default' up with 'virtualbox' provider...
       ==> default: Importing base box 'bento/centos-7.2'...
==> default: Matching MAC address for NAT networking...
       ==> default: Checking if box 'bento/centos-7.2' is up to date...
       ==> default: Setting the name of the VM: kitchen-git-cookbook-default-centos-72_default_1482061251204_56474
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
       Finished creating <default-centos-72> (0m52.92s).
-----> Converging <default-centos-72>...
       Preparing files for transfer
       Preparing dna.json
       Preparing current project directory as a cookbook
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
         to file /tmp/install.sh.9748/metadata.txt
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
       Installing Cookbook Gems:
       Compiling Cookbooks...
       Converging 2 resources
       Recipe: git::default
         * yum_package[git] action install
           - install version 1.8.3.1-6.el7_2.1 of package git
         * log[Well, that was too easy] action write


       Running handlers:
       Running handlers complete
       Chef Client finished, 2/2 resources updated in 32 seconds
       Finished converging <default-centos-72> (0m44.40s).
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
       Finished verifying <default-centos-72> (0m5.49s).
-----> Kitchen is finished. (1m42.99s)
~~~

Nice! We've verified that our cookbook works on Ubuntu 14.04 and CentOS 7.2. Since the CentOS instance will hang out for no good reason, let's kill it for now:

~~~
$ kitchen destroy
-----> Starting Kitchen (v1.14.2)
-----> Destroying <default-ubuntu-1404>...
       Finished destroying <default-ubuntu-1404> (0m0.00s).
-----> Destroying <default-centos-72>...
       ==> default: Forcing shutdown of VM...
       ==> default: Destroying VM and associated drives...
       Vagrant instance <default-centos-72> destroyed.
       Finished destroying <default-centos-72> (0m7.19s).
-----> Kitchen is finished. (0m7.52s)
~~~

Interesting. Test Kitchen tried to destroy both instances, one that was created and the other that was not. Which brings us to another tip with the `kitchen` command:

> **Any `kitchen` subcommand without an instance argument will apply to all instances.**

Let's make sure everything has been destroyed:

~~~
$ kitchen list
Instance             Driver   Provisioner  Verifier  Transport  Last Action    Last Error
default-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
default-centos-72    Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
~~~

There's no production code change so far but we did modify the `.kitchen.yml` file, so let's commit that:

~~~
$ git add .kitchen.yml
$ git commit -m "Add support for CentOS 7.2."
[master f03cff6] Add support for CentOS 7.2.
 1 file changed, 1 insertion(+)
~~~
