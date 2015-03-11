The beginning of your playground
-------------------------------

So you have VirtualBox and Vagrant running; you feel more comfortable with your
_disposable_ vm, so now here's where some of the fun starts.
I have a `Vagrantfile` here that I'm going to use as a base for this book.

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.host_name = 'chef-book'

  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install git-core curl build-essential zlib1g-dev libssl-dev libreadline6-dev libyaml-dev -y

    sudo sed -i '/pam_motd.so/d' /etc/pam.d/sshd
    sudo echo "America/Chicago" > /etc/timezone # because this is the timezone where I live ;)
    sudo dpkg-reconfigure -f noninteractive tzdata
    sudo touch /var/lib/cloud/instance/locale-check.skip

    if ! [ -x /usr/bin/chef ]; then
      cd /tmp
      rm -rf *deb*
      wget https://opscode-omnibus-packages.s3.amazonaws.com/ubuntu/12.04/x86_64/chefdk_0.4.0-1_amd64.deb
      sudo dpkg -i chefdk_0.4.0-1_amd64.deb
    fi
  SHELL
end
```

As you can see it does A LOT. I install the Chef-DK
(which we'll talk about in the next section). Initially I want to use the
`:shell` provisioner so I don't muddy the waters by adding chef-zero to the mix
until we're more familiar with these new tools.

With the [docs](http://docs.vagrantup.com/v2/) and some familiarity with bash
scripting, you should be able to decipher what's going on here. Don't worry,
we'll add/hack on this file as we go through this book. This is just the beginning.

You'll also notice that this provisioning script includes a couple tests to keep
from repeating unnecessary steps if it has already been run.  It is important
for scripts like these to be [idempotent](http://en.wikipedia.org/wiki/Idempotence),
or that they "can be applied multiple times without changing the result beyond
the initial application" as described on wikipedia.

Put this `Vagrantfile` in a new directory, for example `~/vagrant/chef-book/` and do
a `vagrant up`. We will use it in step 5. You should see similar output as in the
previous section when the box is created followed by a lot of additional provisioning
output. That's good. Let it run, grab some coffee or an energy drink, this will take
some time depending on your internet connection:

```bash
[~/vagrant/chef-book] % vagrant up
...
==> default: Running provisioner: shell...
    default: Running: inline script
==> default: stdin: is not a tty
==> default: Ign http://security.ubuntu.com trusty-security InRelease
==> default: Get:1 http://security.ubuntu.com trusty-security Release.gpg [933 B]
==> default: Get:2 http://security.ubuntu.com trusty-security Release [62.0 kB]
==> default: Get:3 http://security.ubuntu.com trusty-security/main Sources [71.9 kB]
==> default: Get:4 http://security.ubuntu.com trusty-security/universe Sources [17.9 kB]
==> default: Get:5 http://security.ubuntu.com trusty-security/main amd64 Packages [221 kB]

[-- snip --]

==> default: Preparing to unpack chefdk_0.4.0-1_amd64.deb ...
==> default: Unpacking chefdk (0.4.0-1) ...
==> default: Setting up chefdk (0.4.0-1) ...
==> default: Thank you for installing Chef Development Kit!
[~/vagrant/chef-book] %
```

Vagrant will automatically attempt to provision your box after it is created for the first time, and when it is recreated after being destroyed.

Move on to [chef-dk](04-chef-dk-install.md)
