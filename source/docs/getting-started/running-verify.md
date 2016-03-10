---
title: Running Kitchen Verify
prev:
  text: "Writing a Test"
  url: "writing-test"
next:
  text: "Running Kitchen Test"
  url: "running-test"
---

Now to put our test to the test. For this we'll use the **verify** subcommand:

~~~
$ kitchen verify default-ubuntu-1204
-----> Starting Kitchen (v1.3.1)
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
       Finished setting up <default-ubuntu-1204> (0m8.72s).
-----> Verifying <default-ubuntu-1204>...
       Suite path directory /tmp/busser/suites does not exist, skipping.
       Uploading /tmp/busser/suites/bats/git_installed.bats (mode=0644)
-----> Running bats test suite
 ✓ git binary is found in PATH
       
       1 test, 0 failures
       Finished verifying <default-ubuntu-1204> (0m1.62s).
-----> Kitchen is finished. (0m10.96s)
$ echo $?
0
~~~

> **All right!**

A few things of note from the output above:

* Notice the `Setting up <default-ubuntu-1204>` line? That's another action called the **Setup Action**. Usually not a big deal for most users but this action is responsible for installing the Busser RubyGem and any test runner plugins required. In our case the `busser-bats` RubyGem was installed which helped to install the bats testing framework.
* The `Verifying <default-ubuntu-1204>` line corresponds to the start of the **Verify Action** and the `✓ git binary is found in PATH` line is output from a bats test run.
* The last command (`echo $?`) is a way to print the exit code of the last run shell command. This shows that the **kitchen** command exited cleanly.

Let's check the status of our instance again:

~~~
$ kitchen list
Instance             Driver   Provisioner  Last Action
default-ubuntu-1204  Vagrant  ChefSolo     Verified
~~~

Quick, commit!

~~~
$ git add test/integration/default/bats/git_installed.bats
$ git commit -m "Add bats test for default suite."
[master 69586ef] Add bats test for default suite.
 1 file changed, 6 insertions(+)
 create mode 100644 test/integration/default/bats/git_installed.bats
~~~

So what would a failing test look like? Let's see. Open `test/integration/default/bats/git_installed.bats` in your editor of choice and edit the test so that we're looking for the `gittt` command:

~~~sh
#!/usr/bin/env bats

@test "gittt binary is found in PATH" {
  run which gittt
  [ "$status" -eq 0 ]
}
~~~

And re-run the **verify** subcommand:

~~~
$ kitchen verify default-ubuntu-1204
-----> Starting Kitchen (v1.3.1)
-----> Verifying <default-ubuntu-1204>...
       Removing /tmp/busser/suites/bats
       Uploading /tmp/busser/suites/bats/git_installed.bats (mode=0644)
-----> Running bats test suite
 ✗ gittt binary is found in PATH
          (in test file /tmp/busser/suites/bats/git_installed.bats, line 5)
            `[ "$status" -eq 0 ]' failed
       
       1 test, 1 failure
       !!!!!! Command [/tmp/busser/vendor/bats/bin/bats /tmp/busser/suites/bats] exit code was 1
>>>>>> Verify failed on instance <default-ubuntu-1204>.
>>>>>> Please see .kitchen/logs/default-ubuntu-1204.log for more details
>>>>>> ------Exception-------
>>>>>> Class: Kitchen::ActionFailed
>>>>>> Message: SSH exited (1) for command: [sh -c '
BUSSER_ROOT="/tmp/busser" GEM_HOME="/tmp/busser/gems" GEM_PATH="/tmp/busser/gems" GEM_CACHE="/tmp/busser/gems/cache" 
export BUSSER_ROOT GEM_HOME GEM_PATH GEM_CACHE

sudo -E /tmp/busser/bin/busser test
']
>>>>>> ----------------------
$ echo $?
10
~~~

Not quite as pretty and happy looking. Thanks to our well-crafted test case name our bats test is telling us what is up: `✗ gittt binary is found in PATH`. Yep, not a surprise. Also note that the exit code is no longer **0** but is **10**.

A quick revert back to our clean code gets us back to green:

~~~
$ git checkout test/integration/default/bats/git_installed.bats
$ kitchen verify default-ubuntu-1204
-----> Starting Kitchen (v1.3.1)
-----> Verifying <default-ubuntu-1204>...
       Removing /tmp/busser/suites/bats
       Uploading /tmp/busser/suites/bats/git_installed.bats (mode=0644)
-----> Running bats test suite
 ✓ git binary is found in PATH
       
       1 test, 0 failures
       Finished verifying <default-ubuntu-1204> (0m1.73s).
-----> Kitchen is finished. (0m2.31s)
~~~

It's worth pointing out that Test Kitchen is freshly uploading all your tests files for **every** invocation of the **Verify Action**. Another example of Test Kitchen optimizing for **freshness of code and configuration over speed**.
