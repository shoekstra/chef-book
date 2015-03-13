A simple cookbook
=================

First off, let's talk about the structure of how chef takes its instructions. 
An instruction is called a _resource_ and it is a single thing Chef can control,
for example a package to be installed or a template for creating a config file.
Several resources can be collectively expressed in something called a _recipe_
and a collection of recipes can be in a cookbook. Pretty straight forward eh?
As I said in the previous section, recipes are top down compiled bits of
software, just like if you are reading a cookbook in real life. (enter a joke
here about screwing up a food recipe).

A first recipe
--------------

Before we jump into a cookbook, let's look at creating a simple recipe.

So, you're still logged into your vm as root?  Create a file called `whatever.rb`:

```bash
root@chef-book:~# nano whatever.rb
```

Let's add this bit of ruby, this will be our recipe:

```rb
file '/tmp/x.txt' do
  content 'hello world'
end
```

Save that file, and now run `chef-client`:

```bash
root@chef-book:~# chef-client -z whatever.rb
[2015-03-13T09:10:57-05:00] WARN: No cookbooks directory found at or above current directory.  Assuming /root.
Starting Chef Client, version 12.0.3
resolving cookbooks for run list: []
Synchronizing Cookbooks:
Compiling Cookbooks...
[2015-03-13T09:11:03-05:00] WARN: Node chef-book has an empty run list.
Converging 1 resources
Recipe: @recipe_files::/root/whatever.rb
  * file[/tmp/x.txt] action create
    - create new file /tmp/x.txt
    - update content in file /tmp/x.txt from none to b94d27
    --- /tmp/x.txt	2015-03-13 09:11:03.364182686 -0500
    +++ /tmp/.x.txt20150313-7186-1mjxr4c	2015-03-13 09:11:03.364182686 -0500
    @@ -1 +1,2 @@
    +hello world

Running handlers:
Running handlers complete
Chef Client finished, 1/1 resources updated in 6.312101149 seconds
```

Now you can verify that there's a new file, `/tmp/x.txt`.  

Does it contain what you expect?

Let's make things easier
------------------------

So the next thing we are going to do is make our lives a little easier. You 
already have a provisioned box with `chef-client` on it, so let's write a 
wrapper script to call `chef-client` so we only have to run one command to 
"converge" the cookbook on a new box. 
Convergence is what chef calls the process of applying the list of recipes that 
you want to run.

Go ahead and create a directory that will be your core working directory. 
`core` is probably a good term.

```bash
root@chef-book:~# mkdir core
root@chef-book:~# cd core
root@chef-book:~/core#
```

converge.sh
-----------

Go ahead and create a new file in your text editor called `converge.sh` 
containing the following:

```bash
#!/bin/bash

chef-client -z -j core.json
```

Go ahead and run `chmod +x converge.sh` to make it executable, then run it.

```bash
root@chef-book:~/core# chmod +x converge.sh
root@chef-book:~/core# ./converge.sh
[2015-03-13T09:11:55-05:00] WARN: No cookbooks directory found at or above current directory.  Assuming /root/core.
[2015-03-13T09:11:55-05:00] FATAL: Cannot load configuration from core.json
root@chef-book:~#
```

Perfect, we are ready to start the next part.

core.json
---------

Open up another text editor to create `core.json` and insert the following:

```json
{
    "run_list": [ "recipe[base::default]" ]
}
```

This is the "run_list" for `chef-client`.  It tells `chef-client` to use the 
`base` cookbook and run the `default` recipe. You can have as long of a 
run_list as you want, but let's start with a single recipe for now.

Go ahead and run `./converge.sh` again, the output should be different:

```bash
root@chef-book:~/core# ./converge.sh
[2015-03-13T09:13:08-05:00] WARN: No cookbooks directory found at or above current directory.  Assuming /root/core.
Starting Chef Client, version 12.0.3
resolving cookbooks for run list: ["base::default"]

================================================================================
Error Resolving Cookbooks for Run List:
================================================================================

Missing Cookbooks:
------------------
No such cookbook: base

Expanded Run List:
------------------
* base::default


Running handlers:
[2015-03-13T09:13:14-05:00] ERROR: Running exception handlers
Running handlers complete
[2015-03-13T09:13:14-05:00] ERROR: Exception handlers complete
[2015-03-13T09:13:14-05:00] FATAL: Stacktrace dumped to /root/.chef/local-mode-cache/cache/chef-stacktrace.out
Chef Client failed. 0 resources updated in 6.22291079 seconds
[2015-03-13T09:13:14-05:00] ERROR: undefined method `cheffish' for nil:NilClass
[2015-03-13T09:13:16-05:00] FATAL: Chef::Exceptions::ChildConvergeError: Chef run process exited unsuccessfully (exit code 1)
root@chef-book:~/core#
```

The bit that should that stand out is:

```
Missing Cookbooks:
------------------
No such cookbook: base
```

And that's expected, you haven't created one yet!

base cookbook
-------------

Ok, you are at `~/core` right? Good, go ahead and type 
`mkdir -p cookbooks/base/recipes/` and `cd` to that directory.

```bash
root@chef-book:~/core# mkdir -p cookbooks/base/recipes/
root@chef-book:~/core# cd cookbooks/base/recipes/
root@chef-book:~/core/cookbooks/base/recipes#
```

Now we need to create the `default.rb` file. Open up your favorite text 
editor and write the following. Now you'll notice that I'm using `nano` here, 
it's not my favorite, far be it, but this is to show the first installation of 
software.

```bash
root@chef-book:~/core/cookbooks/base/recipes# nano default.rb
```

```ruby
package 'vim'
```

Now logically this will install `vim` right? Yep, and we're about to see that.

To ensure this test works, let's uninstall vim from our vagrant box first:

```bash
root@chef-book:~/core/cookbooks/base/recipes# apt-get -y -qq purge vim
(Reading database ... 108024 files and directories currently installed.)
Removing vim (2:7.4.052-1ubuntu3) ...
update-alternatives: using /usr/bin/vim.tiny to provide /usr/bin/vi (vi) in auto mode
update-alternatives: using /usr/bin/vim.tiny to provide /usr/bin/view (view) in auto mode
update-alternatives: using /usr/bin/vim.tiny to provide /usr/bin/ex (ex) in auto mode
update-alternatives: using /usr/bin/vim.tiny to provide /usr/bin/rview (rview) in auto mode
root@chef-book:~/core/cookbooks/base/recipes#
```

Go ahead and go up to `~/core`, and run `./converge.sh`, 
you should see something like this:

```bash
root@chef-book:~/core/cookbooks/base/recipes# cd ~/core
root@chef-book:~/core# ./converge.sh
Starting Chef Client, version 12.0.3
resolving cookbooks for run list: ["base::default"]
Synchronizing Cookbooks:
  - base
Compiling Cookbooks...
Converging 1 resources
Recipe: base::default
  * apt_package[vim] action install
    - install version 2:7.4.052-1ubuntu3 of package vim

Running handlers:
Running handlers complete
Chef Client finished, 1/1 resources updated in 6.989714842 seconds
root@chef-book:~/core#
```

Congratulations! You have successfully installed the greatest editor using 
`chef-client`! Don't believe me? type `vim`. Go ahead and run `./converge.sh` 
again, it should look like this:

```bash
root@chef-book:~/core# ./converge.sh
Starting Chef Client, version 12.0.3
resolving cookbooks for run list: ["base::default"]
Synchronizing Cookbooks:
  - base
Compiling Cookbooks...
Converging 1 resources
Recipe: base::default
  * apt_package[vim] action install (up to date)

Running handlers:
Running handlers complete
Chef Client finished, 0/1 resources updated in 6.199481804 seconds
```

This is important, as you can see it didn't _reinstall_ it. It just checked 
that `vim` was installed and then it moved on. Badass.

If you want to skip ahead, check out the 
[resources](http://docs.opscode.com/resource.html) and see what cool things you 
can do. Don't worry I'll walk y'all through some more.

So let's add a couple more packages to our base recipe 
(`~/core/cookbooks/base/recipes/default.rb`). 
There are two ways you can do this. One is to just add them line by line, like:

```ruby
package 'vim'
package 'ntp'
package 'build-essential'
```

Or you can do:

```ruby
%w{vim ntp build-essential}.each do |pkg|
  package pkg do
    action :install
  end
end
```

Both have the same effect. The second example demonstrates that the recipe is *just ruby*, so you can take advantage of array literals and iterators, and also that you can specify and `action` for the package resource (we choose the default `install` action).

Go ahead and `cd ~/core/` and run `./converge.sh` again.

```bash
root@chef-book:~/core# ./converge.sh
Starting Chef Client, version 12.0.3
resolving cookbooks for run list: ["base::default"]
Synchronizing Cookbooks:
  - base
Compiling Cookbooks...
Converging 3 resources
Recipe: base::default
  * apt_package[vim] action install (up to date)
  * apt_package[ntp] action install
    - install version 1:4.2.6.p5+dfsg-3ubuntu2.14.04.2 of package ntp
  * apt_package[build-essential] action install (up to date)

Running handlers:
Running handlers complete
Chef Client finished, 1/3 resources updated in 13.328761076 seconds
```

Congrats! You can now install packages via Chef and confirm that they are there.

Next up is the Chef version of the Puppet 
"[trifecta](http://docs.puppetlabs.com/puppet_core_types_cheatsheet.pdf)".  
In the Puppet world there is a phrase: "Package/file/service: Learn it, live it, love it. 
If you can only do this, you can still do a lot." Which is very true.

Let's try to leverage this idea with Chef. In the real world you probably don't want to log into your boxes as `vagrant ssh` right? So let's create a `deployer` user.  
I'll first start out with the Chef trifecta, then move to the user account.

Chef Trifecta
-------------

If you looked at the cheat sheet above you would have seen:

```puppet
package { 'openssh-server':
 ensure => installed,
}

file { '/etc/ssh/ssh_config':
 source  => 'puppet:///modules/sshd/ssh_config',
 owner   => 'root',
 group   => 'root',
 mode    => '0640',
 notify  => Service['sshd'], # sshd will restart whenever you edit this file.
 require => Package['openssh-server'],
}

service { 'sshd':
 ensure     => running,
 enable     => true,
 hasstatus  => true,
 hasrestart => true,
}
```

Let's create this in Chef. I'm going to create another recipe and link it to the
Chef run. Here we go:

```bash
root@chef-book:~/core# vim cookbooks/base/recipes/ssh.rb
```

```ruby
package 'openssh-server' do
  action :install
end

cookbook_file '/etc/ssh/ssh_config' do
  source 'ssh_config'
  owner 'root'
  group 'root'
  mode '0640'
  notifies :reload, 'service[ssh]'
end

service 'ssh' do
  provider Chef::Provider::Service::Upstart
  action [:enable, :start]
  supports :status => true, :restart => true
end
```

*Note*: Because we are using Ubuntu 14.04 Trusty Tahr, you will need to include 
the `provider Chef::Provider::Service::Upstart` line to `service "ssh"` block to
call the Upstart provider instead of the shell as in previous versions.

Oh, you'll need an `ssh_config` file. Copying it from `/etc/ssh/` will be fine.

```bash
root@chef-book:~/core# mkdir -p cookbooks/base/files/default
root@chef-book:~/core# cp /etc/ssh/ssh_config cookbooks/base/files/default/
```

As you can see, `cookbook_file` is the stanza that tells Chef to put *this
file* in *this location* with *these settings* and *this is the source*. You should 
have noticed that you created a `files/default` directory. That is the first 
location that `cookbook_file` checks. You can create different directories in 
`files/` like `ubuntu` or `ubuntu12.04` or `redhat` so you can have a different 
format for different platforms. Now I should mention that `files` is just for
_static_ files, not templatized files. We'll get to those in a bit.

Go ahead and run your `./converge` again; you should see something like this:

```bash
root@chef-book:~/core# ./converge.sh
Starting Chef Client, version 12.0.3
resolving cookbooks for run list: ["base::default"]
Synchronizing Cookbooks:
  - base
Compiling Cookbooks...
Converging 3 resources
Recipe: base::default
  * apt_package[vim] action install (up to date)
  * apt_package[ntp] action install (up to date)
  * apt_package[build-essential] action install (up to date)

Running handlers:
Running handlers complete
Chef Client finished, 0/3 resources updated in 6.246738599 seconds
```

Ah, you got me. We didn't add the ssh recipe to the default run, did we? Go 
ahead and open up `cookbooks/base/recipes/default.rb` and add the following:

```ruby
%w{vim ntp build-essential}.each do |pkg|
   package pkg do
     action :install
  end
end

include_recipe 'base::ssh'
```

Ok, now go ahead and run `./converge.sh` again. You should see something like this:

```bash
Starting Chef Client, version 12.0.3
resolving cookbooks for run list: ["base::default"]
Synchronizing Cookbooks:
  - base
Compiling Cookbooks...
Converging 6 resources
Recipe: base::default
  * apt_package[vim] action install (up to date)
  * apt_package[ntp] action install (up to date)
  * apt_package[build-essential] action install (up to date)
Recipe: base::ssh
  * apt_package[openssh-server] action install (up to date)
  * cookbook_file[/etc/ssh/ssh_config] action create
    - change mode from '0644' to '0640'
  * service[ssh] action enable (up to date)
  * service[ssh] action start (up to date)
  * service[ssh] action reload
    - reload service service[ssh]

Running handlers:
Running handlers complete
Chef Client finished, 2/8 resources updated in 6.484312866 seconds
```

Ok, so let's take this one step farther. Go ahead and open up `cookbooks/base/files/default/ssh_config` and put a comment at the top of the file. Diff the source file in the cookbook with the real file on disk.

```bash
root@chef-book:~/core# vim cookbooks/base/files/default/ssh_config
root@chef-book:~/core# diff -u /etc/ssh/ssh_config cookbooks/base/files/default/ssh_config
--- /etc/ssh/ssh_config	2014-05-12 11:04:32.000000000 -0500
+++ cookbooks/base/files/default/ssh_config	2015-03-13 09:21:06.004158399 -0500
@@ -1,4 +1,4 @@
-
+# this is a comment i added at the top
 # This is the ssh client system-wide configuration file.  See
 # ssh_config(5) for more information.  This file provides defaults for
 # users, and the values can be changed in per-user configuration files
 root@chef-book:~/core#
```

Go ahead and run the `./converge.sh` again. You should see no difference now.

```bash
root@chef-book:~/core# diff -u /etc/ssh/ssh_config cookbooks/base/files/default/ssh_config
root@chef-book:~/core#
```

Nice, we now have the ability to install a package, install a config file, and confirm that the service is up and running.

Ok, if you have any chef knowledge, you are probably wondering why we didn't add this to the `run_list`. That's a great question, why not? I wanted to demonstrate how different recipes can call other recipes, even from other cookbooks. If you want to use the `run_list` way, 
all you have to do is add it to `~/core/core.json`:

```json
{
    "run_list": [ "recipe[base::default]","recipe[base::ssh]" ]
}
```

Don't get me wrong this is extremely important, but I was going to revisit this when we started adding external cookbooks. You made me jump my gun. :P

Now, let's go on to the deployer user.

deployer user
-------------

If you want to [read](http://docs.opscode.com/resource_user.html) about this, here's the [link](http://docs.opscode.com/resource_user.html).

Now first things first, we need to create ssh keys, or you can use your own. If you don't know what ssh keys are, you  could start [here](https://wiki.archlinux.org/index.php/SSH_Keys). If this doesn't make sense....sigh, you probably shouldn't have read this far.

Since I'm lazy, I'll set up passwordless keys with root on the vm that I created:

```bash
root@chef-book:~/core# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
87:c2:46:95:9a:bc:a6:d9:88:a3:fa:68:e0:c1:c3:75 root@chef-book
The key's randomart image is:
+--[ RSA 2048]----+
|        ..       |
|       ..        |
|     ..o         |
|   . E+  .       |
|o . . +.S .      |
|.=   .o. .       |
|o o. *           |
| +o + .          |
|*o..             |
+-----------------+
root@chef-book:~#

```

Great, now we go to your base cookbook recipes and can define the deployer user.

```
root@chef-book:~/core# vim cookbooks/base/recipes/deployer.rb
```

Let's start out the file:

```ruby
group 'deployer' do
  gid 15000
  action :create
end

user 'deployer' do
  supports :manage_home => true
  comment 'D-Deployer'
  uid 15000
  gid 15000
  home '/home/deployer'
  shell '/bin/bash'
end

directory '/home/deployer/.ssh' do
  owner 'deployer'
  group 'deployer'
  action :create
end

cookbook_file '/home/deployer/.ssh/authorized_keys' do
  source 'deployer_key.pub'
  owner 'deployer'
  group 'deployer'
  action :create_if_missing
  mode '0600'
end
```

Well that seems pretty straight forward right? Walk through it, the `directory` is new, but other than that we've used everything else. Next copy the ssh key you created in a previous step into the cookbook's `files/default/` directory as `deployer_key.pub`.

```bash
root@chef-book:~/core# cp ~/.ssh/id_rsa.pub cookbooks/base/files/default/deployer_key.pub
```

And let's converge.

```bash
root@chef-book:~/core# ./converge.sh
Starting Chef Client, version 12.0.3
resolving cookbooks for run list: ["base::default", "base::ssh"]
Synchronizing Cookbooks:
  - base
Compiling Cookbooks...
Converging 6 resources
Recipe: base::default
  * apt_package[vim] action install (up to date)
  * apt_package[ntp] action install (up to date)
  * apt_package[build-essential] action install (up to date)
Recipe: base::ssh
  * apt_package[openssh-server] action install (up to date)
  * cookbook_file[/etc/ssh/ssh_config] action create (up to date)
  * service[ssh] action enable (up to date)
  * service[ssh] action start (up to date)

Running handlers:
Running handlers complete
Chef Client finished, 0/7 resources updated in 6.329689571 seconds
```

Doh! We did it again: we didn't add it to the recipe. This time, let's add it to the `run_list` instead.

```bash
root@chef-book:~/core# vim core.json
```

And change the file to look like this:

```json
{
    "run_list": [ "recipe[base::default]","recipe[base::ssh]","recipe[base::deployer]" ]
}
```

Now `./converge` and you should see something like this:

```bash
root@chef-book:~/core# ./converge.sh
Starting Chef Client, version 12.0.3
resolving cookbooks for run list: ["base::default", "base::ssh", "base::deployer"]
Synchronizing Cookbooks:
  - base
Compiling Cookbooks...
Converging 10 resources
Recipe: base::default
  * apt_package[vim] action install (up to date)
  * apt_package[ntp] action install (up to date)
  * apt_package[build-essential] action install (up to date)
Recipe: base::ssh
  * apt_package[openssh-server] action install (up to date)
  * cookbook_file[/etc/ssh/ssh_config] action create (up to date)
  * service[ssh] action enable (up to date)
  * service[ssh] action start (up to date)
Recipe: base::deployer
  * group[deployer] action create
    - create group[deployer]
  * user[deployer] action create
    - create user deployer
  * directory[/home/deployer/.ssh] action create
    - create new directory /home/deployer/.ssh
    - change owner from '' to 'deployer'
    - change group from '' to 'deployer'
  * cookbook_file[/home/deployer/.ssh/authorized_keys] action create_if_missing
    - create new file /home/deployer/.ssh/authorized_keys
    - update content in file /home/deployer/.ssh/authorized_keys from none to e7ece7
    --- /home/deployer/.ssh/authorized_keys	2015-03-13 09:25:02.572725706 -0500
    +++ /home/deployer/.ssh/.authorized_keys20150313-10293-piulsx	2015-03-13 09:25:02.572725706 -0500
    @@ -1 +1,2 @@
    +ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDZZo88bmSvVm9hBi6BDTkBBju8zQVsuUUumDP40YLQSgLLOnRzKaSXos8WMmSfzF4w9crGARfa7VsbZbV8zHtBSDsk0wMl9USfcp4zxUScFeOvpkSa6eS4TtQQahGz0uzhQyroi09a9kFq6NY6QqQXvWoAeaaDHUZgIvT1xPBAJLC5Wk9kTLw6Fx2moubb0l0fYciSP2cwehSCPKcZdyDPmBW5REnrhqa9DuJtKPP0P2ojssFANNnmaTq2u3c0WqIVSxVAwZLqTFh8CGwnzyxckz2kG+6/2QZ85gXjysLaXnyCRhSAs/fu3N6Dg370rSjZtpbg/X/miu1ihVQT3S79 root@chef-book
    - change mode from '' to '0600'
    - change owner from '' to 'deployer'
    - change group from '' to 'deployer'

Running handlers:
Running handlers complete
Chef Client finished, 4/11 resources updated in 6.403209094 seconds
root@chef-book:~/core#
```

Now let's test this out.

```bash
root@chef-book:~/core# ssh deployer@localhost
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is 05:4d:8d:e9:ae:da:3f:ce:a8:69:cc:0e:50:b8:6d:2a.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
deployer@chef-book:~$
```

Badass! Now you can create a default deployer user and change things around as needed. This will be much more useful later on in the book when we start spinning machines up in the "cloud".

Move on to 
[Running Vagrant provisioning vs a local chef-zero run](07-vagrant-provisioning-vs-local-chef-zero.md)
