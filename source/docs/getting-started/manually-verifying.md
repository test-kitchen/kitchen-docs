---
title: "Manually Verifying"
prev:
  text: "Running Kitchen Converge"
  url: "running-converge"
next:
  text: "Writing a Test"
  url: "writing-test"
---

If you're a skeptical person then you might be asking:

> "How can we be sure that Git was actually installed?"

Let's verify this right now.

Test Kitchen has a **login** subcommand for just these kinds of situations:

~~~
$ kitchen login default-ubuntu-1404
Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-103-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

...

Last login: Sun Dec 18 10:47:20 2016 from 10.0.2.2
vagrant@default-ubuntu-1404:~$
~~~

As you can see by the prompt above we are now in the `default-ubuntu-1204` instance. We'll denote the prompt in an instance with `# ` for clarity. Now to check if Git is installed:

~~~
# which git
/usr/bin/git
# git --version
git version 1.9.1
~~~

Rockin. Now we can exit out back to our workstation:

~~~
# exit
logout
Connection to 127.0.0.1 closed.
~~~

Feel free to use the **login** subcommand any time you have the urge to poke around, uninstall packages, turn off services, grep logs, etc. Go to town, this is a sandbox and isn't production after all.
