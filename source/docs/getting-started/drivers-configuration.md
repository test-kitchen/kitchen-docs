---
title: "Optional Driver Configuration"
---

## Vagrant Driver

Sometimes you'd like to add another NIC to your vm, or bump up the memory. You might have a `forwarded_port` you'd like
to add also. The following snippet is a couple suggestions how. You can even turn on the `auto_correct` if you think
you'll need it.

~~~yaml
  name: vagrant
  network:
  - ["forwarded_port", {guest: 80, host: 8080, auto_correct: true}]
  - ["forwarded_port", {guest: 443, host: 8443}]
  - ["private_network", {ip: "10.0.0.1"}]
  customize:
    cpus: 2
    memory: 4096
~~~

As you can see this very close to the [Vagrantfile forwarded_port](https://docs.vagrantup.com/v2/networking/forwarded_ports.html) and
works exactly like how you'd expect it to.

Doubly, as you can see the [Vagrantfile private_network](https://docs.vagrantup.com/v2/networking/private_network.html) looks just
like you'd expect it to.

If you would like more information the [kitchen-vagrant](https://github.com/test-kitchen/kitchen-vagrant) github page has more.
