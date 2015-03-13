Running Vagrant provisioning vs a local chef run
------------------------------------------------

With a working `chef` converge, 
this is a good time to talk about using local Chef 
and running Chef outside via Vagrant. As I am developing/testing a cookbook I 
personally prefer the local Chef install. 
It allows for a faster iterations which is great. 
But when you get your cookbook where you want it to be,`vagrant provision` 
is the way to go. For fun let's convert the `base` cookbook we wrote to the
`Vagrant` file method.

Luckily Vagrant has mounted the `/vagrant` directory where you did the `vagrant up` from. Go ahead and do something like the following:

```bash
root@chef-book:~/core# cp -r cookbooks/ /vagrant/
root@chef-book:~/core#
```

This will copy the cookbooks directory from your `~/core` working directory into the host file system. Go ahead and drop out of the vm now.

```bash
root@chef-book:~/core# exit
logout
vagrant@chef-book:~$ exit
logout
Connection to 127.0.0.1 closed.
[~/vagrant/chef-book] % ls
Vagrantfile cookbooks
```

As you can see your `cookbooks` directory is there.  

## Vagrant Chef Zero

Chef Zero is new, and therefore we need to install a 
[custom provisioner][v-c-z] for Vagrant:

```
vagrant plugin install vagrant-chef-zero
```

It'll give you a message like this:

```
Installing the 'vagrant-chef-zero' plugin. This can take a few minutes...
Installed the plugin 'vagrant-chef-zero (0.7.1)'!
```

Lord it over your friends.  They will respect you.

[v-c-z]: https://github.com/andrewgross/vagrant-chef-zero

## Vagrant File

Next you'll need to open up the `Vagrantfile` and add the run_list to it so chef-solo can do its magic.

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

chefdk = 'chefdk_0.4.0-1_amd64.deb'

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.host_name = 'chef-book'

  # config.vm.provision "shell", inline: <<-SHELL
  #   echo "Adding brightbox PPA ..."
  #   sudo add-apt-repository -y ppa:brightbox/ruby-ng > /dev/null 2>&1
  #   echo "Updating repositories ... "
  #   sudo apt-get -qq update > /dev/null 2>&1
  #   echo "Installing some packages ..."
  #   sudo apt-get install -qq -y git-core curl build-essential zlib1g-dev libssl-dev libreadline6-dev libyaml-dev ruby2.1 ruby2.1-dev > /dev/null 2>&1
  #
  #   sudo echo "America/Chicago" > /etc/timezone # because this is the timezone where I live ;)
  #   sudo dpkg-reconfigure -f noninteractive tzdata
  #
  #   sudo sed -i '/pam_motd.so/d' /etc/pam.d/sshd
  #   sudo touch /var/lib/cloud/instance/locale-check.skip
  #
  #   if ! [ -x /usr/bin/chef ]; then
  #     cd /tmp
  #     rm -rf *deb*
  #     echo "Downloading #{chefdk} ..."
  #     wget -nv "https://opscode-omnibus-packages.s3.amazonaws.com/ubuntu/12.04/x86_64/#{chefdk}"
  #     sudo dpkg -i "#{chefdk}"
  #   fi
  # SHELL

  config.vm.provision "chef_solo" do |chef|
    chef.add_recipe "base"
  end
end
```

Notice I commented out the `:shell` provisioning and added the `chef_solo` provisioner. Save this, exit the editor, and run `vagrant reload` then `vagrant provision` and you should see something like this:

```bash
[~/vagrant/chef-book] % vagrant reload
==> default: Attempting graceful shutdown of VM...
==> default: Checking if box 'ubuntu/trusty64' is up to date...
==> default: Clearing any previously set forwarded ports...
==> default: Fixed port collision for 22 => 2222. Now on port 2201.
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 => 2201 (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2201
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection timeout. Retrying...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Setting hostname...
==> default: Mounting shared folders...
    default: /vagrant => ~/vagrant/chef-book
    default: /tmp/vagrant-chef/a4beeb7413868f480c1b0e1a026865d3/cookbooks => ~/vagrant/chef-book/cookbooks
==> default: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> default: to force provisioning. Provisioners marked to run always will still run.
[~/vagrant/chef-book] % vagrant provision
==> default: Running provisioner: chef_solo...
    default: Installing Chef (latest)...
Generating chef JSON and uploading...
==> default: Running chef-solo...
==> default: stdin: is not a tty
==> default: [2015-03-13T09:29:52-05:00] INFO: Forking chef instance to converge...
==> default: [2015-03-13T09:29:52-05:00] INFO: *** Chef 12.1.1 ***
==> default: [2015-03-13T09:29:52-05:00] INFO: Chef-client pid: 1903
==> default: [2015-03-13T09:29:58-05:00] INFO: Setting the run_list to ["recipe[base]"] from CLI options
==> default: [2015-03-13T09:29:58-05:00] INFO: Run List is [recipe[base]]
==> default: [2015-03-13T09:29:58-05:00] INFO: Run List expands to [base]
==> default: [2015-03-13T09:29:58-05:00] INFO: Starting Chef Run for chef-book
==> default: [2015-03-13T09:29:58-05:00] INFO: Running start handlers
==> default: [2015-03-13T09:29:58-05:00] INFO: Start handlers complete.
==> default: [2015-03-13T09:29:58-05:00] INFO: Chef Run complete in 0.239408252 seconds
==> default: [2015-03-13T09:29:58-05:00] INFO: Skipping removal of unused files from the cache
==> default: [2015-03-13T09:29:58-05:00] INFO: Running report handlers
==> default: [2015-03-13T09:29:58-05:00] INFO: Report handlers complete
[~/vagrant/chef-book] %
```

Hopefully you get the beauty of the built in provisioner. You can select the cookbooks that you'd like to test out, edit the run_list and run the provisioning to get the box how you want. Don't get me wrong there's much more to it, the [docs](http://docs.vagrantup.com/v2/provisioning/chef_solo.html) have a ton more, but this is just a basic example.

Now let's move on to your most important tool as a chef; [knife](../part3/08-knife.md).
