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
$ kitchen verify default-ubuntu-1404
-----> Starting Kitchen (v1.14.2)
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
       Finished verifying <default-ubuntu-1404> (0m3.32s).
-----> Kitchen is finished. (0m5.46s)
$ echo $?
0
~~~

> **All right!**

A few things of note from the output above:

* Notice the `Setting up <default-ubuntu-1404>` line? That's another action called the **Setup Action**. Usually not a big deal for most users but this action is responsible for installing the Busser RubyGem and any test runner plugins required. In our case the `busser-bats` RubyGem was installed which helped to install the bats testing framework.
* The `Verifying <default-ubuntu-1404>` line corresponds to the start of the **Verify Action** and the `✓ git binary is found in PATH` line is output from a bats test run.
* The last command (`echo $?`) is a way to print the exit code of the last run shell command. This shows that the **kitchen** command exited cleanly.

Let's check the status of our instance again:

~~~
$ kitchen list
Instance             Driver   Provisioner  Verifier  Transport  Last Action  Last Error
default-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        Verified     <None>
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
$ kitchen verify default-ubuntu-1404
-----> Starting Kitchen (v1.14.2)
-----> Verifying <default-ubuntu-1404>...
       Preparing files for transfer
-----> Busser installation detected (busser)
       Installing Busser plugins: busser-bats
       Plugin bats already installed
       Removing /tmp/verifier/suites/bats
       Transferring files to <default-ubuntu-1404>
-----> Running bats test suite
 ✗ gittt binary is found in PATH
          (in test file /tmp/verifier/suites/bats/git_installed.bats, line 5)
            `[ "$status" -eq 0 ]' failed

       1 test, 1 failure
       !!!!!! Command [/tmp/verifier/vendor/bats/bin/bats /tmp/verifier/suites/bats] exit code was 1
>>>>>> ------Exception-------
>>>>>> Class: Kitchen::ActionFailed
>>>>>> Message: 1 actions failed.
>>>>>>     Verify failed on instance <default-ubuntu-1404>.  Please see .kitchen/logs/default-ubuntu-1404.log for more details
>>>>>> ----------------------
>>>>>> Please see .kitchen/logs/kitchen.log for more details
>>>>>> Also try running `kitchen diagnose --all` for configuration
$ echo $?
20
~~~

Not quite as pretty and happy looking. Thanks to our well-crafted test case name our bats test is telling us what is up: `✗ gittt binary is found in PATH`. Yep, not a surprise. Also note that the exit code is no longer **0** but is **20**.

A quick revert back to our clean code gets us back to green:

~~~
$ git checkout test/integration/default/bats/git_installed.bats
$ kitchen verify default-ubuntu-1404
-----> Starting Kitchen (v1.14.2)
-----> Verifying <default-ubuntu-1404>...
       Preparing files for transfer
-----> Busser installation detected (busser)
       Installing Busser plugins: busser-bats
       Plugin bats already installed
       Removing /tmp/verifier/suites/bats
       Transferring files to <default-ubuntu-1404>
-----> Running bats test suite
 ✓ git binary is found in PATH

       1 test, 0 failures
       Finished verifying <default-ubuntu-1404> (0m3.50s).
-----> Kitchen is finished. (0m3.63s)
~~~

It's worth pointing out that Test Kitchen is freshly uploading all your tests files for **every** invocation of the **Verify Action**. Another example of Test Kitchen optimizing for **freshness of code and configuration over speed**.
