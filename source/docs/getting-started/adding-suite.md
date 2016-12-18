---
title: Adding a Suite
prev:
  text: "Adding a New Feature"
  url: "adding-feature"
next:
  text: "Writing a Server Test"
  url: "writing-server-test"
---

We're going to call our new Test Kitchen Suite `"server"` by opening `.kitchen.yml` in your editor of choice so that it looks similar to:

~~~yaml
---
driver:
  name: vagrant

provisioner:
  name: chef_solo

platforms:
  - name: ubuntu-12.04
  - name: ubuntu-10.04
  - name: centos-6.4

suites:
  - name: default
    run_list:
      - recipe[git::default]
    attributes:
  - name: server
    run_list:
      - recipe[git::server]
    attributes:
~~~

Now run `kitchen list` to see our new suite in action:

~~~
$ kitchen list
Instance             Driver   Provisioner  Verifier  Transport  Last Action    Last Error
default-ubuntu-1404  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
default-ubuntu-1004  Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
default-centos-72    Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
server-ubuntu-1404   Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
server-ubuntu-1004   Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
server-centos-72     Vagrant  ChefSolo     Busser    Ssh        <Not Created>  <None>
~~~

Woah, we've doubled our number of instances! Yes, that is going to happen. This explosion of test cases is just one reason why testing is hard.
