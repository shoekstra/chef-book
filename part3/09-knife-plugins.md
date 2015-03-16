# knife plugins

There are a TON more of [knife-plugins](http://docs.opscode.com/plugin_knife.html), then officially posted on the docs site.  [Github](https://github.com/search?q=knife+plugin&type=Repositories&ref=searchresults) is a great place to look but I'm going to focus on 4 here. 

So far all the knife plugins I've seen are ruby gems.  So before you can use any of these be sure you have an ability to use `gem install`.

knife-solo is a great tool to emulate a chef-server with just knife commands, this is a poormans version, but it works extremely well.

knife rackspace is a way to interact directly with the rackspace Openstack api, allowing you to spin up boxes via the commandline and even provision them farther leveraging the chef-server of your choice. I'm only going to show spinning up and down boxes here, we don't have a chef-server running yet right?.....right? ;)

knife ec2, is the amazon version of knife rackspace if you will, there aren't terribly number of differences, but i wanted to make sure you realize that you have to choose which version to run to talk to which back end.

knife spork is a great tool for multiple chefs working with one chef-server. I'm only going to touch on it briefly, being again, we _shouldn't_ have a chef-server running....yet.

## knife solo

So [knife solo](http://matschaffer.github.io/knife-solo/) as I say above is a poor mans chef-server. In a nutshell it takes your local directory/cookbooks, uploads them to your box, and runs chef-solo. This is different than how [chef-server|chef-client] so keep that in mind as you work with it.  Lets get it working :)

First thing first, installation. You can either add it to a Gemfile, or in my case I like just using 'gem install knife-solo`.  Notice the "-" between the name. You should see something like this:

```bash
vagrant@chef-book:~/cookbooks$ sudo gem install knife-solo
Fetching: net-ssh-2.9.3.beta1.gem (100%)
Successfully installed net-ssh-2.9.3.beta1
Fetching: net-scp-1.2.1.gem (100%)
Successfully installed net-scp-1.2.1
Fetching: specinfra-2.19.4.gem (100%)
Successfully installed specinfra-2.19.4
Fetching: multi_json-1.11.0.gem (100%)
Successfully installed multi_json-1.11.0
Fetching: diff-lcs-1.2.5.gem (100%)
Successfully installed diff-lcs-1.2.5
Fetching: rspec-support-3.2.2.gem (100%)
Successfully installed rspec-support-3.2.2
Fetching: rspec-expectations-3.2.0.gem (100%)
Successfully installed rspec-expectations-3.2.0
Fetching: rspec-core-3.2.2.gem (100%)
Successfully installed rspec-core-3.2.2
Fetching: rspec-its-1.2.0.gem (100%)
Successfully installed rspec-its-1.2.0
Fetching: rspec-mocks-3.2.1.gem (100%)
Successfully installed rspec-mocks-3.2.1
Fetching: rspec-3.2.0.gem (100%)
Successfully installed rspec-3.2.0
Fetching: serverspec-2.10.1.gem (100%)
Successfully installed serverspec-2.10.1
Fetching: builder-3.2.2.gem (100%)
Successfully installed builder-3.2.2
Fetching: rspec_junit_formatter-0.2.0.gem (100%)
Successfully installed rspec_junit_formatter-0.2.0
Fetching: plist-3.1.0.gem (100%)
Successfully installed plist-3.1.0
Fetching: method_source-0.8.2.gem (100%)
Successfully installed method_source-0.8.2
Fetching: slop-3.6.0.gem (100%)
Successfully installed slop-3.6.0
Fetching: coderay-1.1.0.gem (100%)
Successfully installed coderay-1.1.0
Fetching: pry-0.10.1.gem (100%)
Successfully installed pry-0.10.1
Fetching: rack-1.6.0.gem (100%)
Successfully installed rack-1.6.0
Fetching: libyajl2-1.2.0.gem (100%)
Building native extensions.  This could take a while...
Successfully installed libyajl2-1.2.0
Fetching: ffi-1.9.6.gem (100%)
Building native extensions.  This could take a while...
Successfully installed ffi-1.9.6
Fetching: ffi-yajl-1.4.0.gem (100%)
Building native extensions.  This could take a while...
Successfully installed ffi-yajl-1.4.0
Fetching: uuidtools-2.1.5.gem (100%)
Successfully installed uuidtools-2.1.5
Fetching: hashie-2.1.2.gem (100%)
Successfully installed hashie-2.1.2
Fetching: mixlib-log-1.6.0.gem (100%)
Successfully installed mixlib-log-1.6.0
Fetching: chef-zero-4.0.gem (100%)
Successfully installed chef-zero-4.0
Fetching: erubis-2.7.0.gem (100%)
Successfully installed erubis-2.7.0
Fetching: highline-1.7.1.gem (100%)
Successfully installed highline-1.7.1
Fetching: net-ssh-gateway-1.2.0.gem (100%)
Successfully installed net-ssh-gateway-1.2.0
Fetching: net-ssh-multi-1.2.0.gem (100%)
Successfully installed net-ssh-multi-1.2.0
Fetching: wmi-lite-1.0.0.gem (100%)
Successfully installed wmi-lite-1.0.0
Fetching: ipaddress-0.8.0.gem (100%)
Successfully installed ipaddress-0.8.0
Fetching: net-dhcp-1.3.2.gem (100%)
Successfully installed net-dhcp-1.3.2
Fetching: mixlib-shellout-2.0.1.gem (100%)
Successfully installed mixlib-shellout-2.0.1
Fetching: mixlib-config-2.1.0.gem (100%)
Successfully installed mixlib-config-2.1.0
Fetching: mixlib-cli-1.5.0.gem (100%)
Successfully installed mixlib-cli-1.5.0
Fetching: systemu-2.6.4.gem (100%)
Successfully installed systemu-2.6.4
Fetching: mime-types-2.4.3.gem (100%)
Successfully installed mime-types-2.4.3
Fetching: ohai-8.1.1.gem (100%)
Successfully installed ohai-8.1.1
Fetching: mixlib-authentication-1.3.0.gem (100%)
Successfully installed mixlib-authentication-1.3.0
Fetching: chef-12.1.1.gem (100%)
Successfully installed chef-12.1.1
Fetching: knife-solo-0.4.2.gem (100%)
Thanks for installing knife-solo!

If you run into any issues please let us know at:
  https://github.com/matschaffer/knife-solo/issues

If you are upgrading knife-solo please uninstall any old versions by
running `gem clean knife-solo` to avoid any errors.

See http://bit.ly/CHEF-3255 for more information on the knife bug
that causes this.
Successfully installed knife-solo-0.4.2
Parsing documentation for builder-3.2.2
Installing ri documentation for builder-3.2.2
Parsing documentation for chef-12.1.1
Installing ri documentation for chef-12.1.1
Parsing documentation for chef-zero-4.0
Installing ri documentation for chef-zero-4.0
invalid options: -SNw2
(invalid options are ignored)
Parsing documentation for coderay-1.1.0
Installing ri documentation for coderay-1.1.0
Parsing documentation for diff-lcs-1.2.5
Installing ri documentation for diff-lcs-1.2.5
Parsing documentation for erubis-2.7.0
Installing ri documentation for erubis-2.7.0
Parsing documentation for ffi-1.9.6
Installing ri documentation for ffi-1.9.6
Parsing documentation for ffi-yajl-1.4.0
Installing ri documentation for ffi-yajl-1.4.0
Parsing documentation for hashie-2.1.2
Installing ri documentation for hashie-2.1.2
Parsing documentation for highline-1.7.1
Installing ri documentation for highline-1.7.1
Parsing documentation for ipaddress-0.8.0
Installing ri documentation for ipaddress-0.8.0
Parsing documentation for knife-solo-0.4.2
Installing ri documentation for knife-solo-0.4.2
Parsing documentation for libyajl2-1.2.0
Installing ri documentation for libyajl2-1.2.0
Parsing documentation for method_source-0.8.2
Installing ri documentation for method_source-0.8.2
Parsing documentation for mime-types-2.4.3
Installing ri documentation for mime-types-2.4.3
Parsing documentation for mixlib-authentication-1.3.0
Installing ri documentation for mixlib-authentication-1.3.0
Parsing documentation for mixlib-cli-1.5.0
Installing ri documentation for mixlib-cli-1.5.0
Parsing documentation for mixlib-config-2.1.0
Installing ri documentation for mixlib-config-2.1.0
Parsing documentation for mixlib-log-1.6.0
Installing ri documentation for mixlib-log-1.6.0
Parsing documentation for mixlib-shellout-2.0.1
Installing ri documentation for mixlib-shellout-2.0.1
Parsing documentation for multi_json-1.11.0
Installing ri documentation for multi_json-1.11.0
Parsing documentation for net-dhcp-1.3.2
unable to convert "\xDF" from ASCII-8BIT to UTF-8 for lib/net/dhcp/oui.txt, skipping
Installing ri documentation for net-dhcp-1.3.2
Parsing documentation for net-scp-1.2.1
Installing ri documentation for net-scp-1.2.1
Parsing documentation for net-ssh-2.9.3.beta1
Installing ri documentation for net-ssh-2.9.3.beta1
Parsing documentation for net-ssh-gateway-1.2.0
Installing ri documentation for net-ssh-gateway-1.2.0
Parsing documentation for net-ssh-multi-1.2.0
Installing ri documentation for net-ssh-multi-1.2.0
Parsing documentation for ohai-8.1.1
Installing ri documentation for ohai-8.1.1
Parsing documentation for plist-3.1.0
Installing ri documentation for plist-3.1.0
Parsing documentation for pry-0.10.1
Installing ri documentation for pry-0.10.1
Parsing documentation for rack-1.6.0
Installing ri documentation for rack-1.6.0
Parsing documentation for rspec-3.2.0
Installing ri documentation for rspec-3.2.0
Parsing documentation for rspec-core-3.2.2
Installing ri documentation for rspec-core-3.2.2
Parsing documentation for rspec-expectations-3.2.0
Installing ri documentation for rspec-expectations-3.2.0
Parsing documentation for rspec-its-1.2.0
Installing ri documentation for rspec-its-1.2.0
Parsing documentation for rspec-mocks-3.2.1
Installing ri documentation for rspec-mocks-3.2.1
Parsing documentation for rspec-support-3.2.2
Installing ri documentation for rspec-support-3.2.2
Parsing documentation for rspec_junit_formatter-0.2.0
Installing ri documentation for rspec_junit_formatter-0.2.0
Parsing documentation for serverspec-2.10.1
Installing ri documentation for serverspec-2.10.1
Parsing documentation for slop-3.6.0
Installing ri documentation for slop-3.6.0
Parsing documentation for specinfra-2.19.4
Installing ri documentation for specinfra-2.19.4
Parsing documentation for systemu-2.6.4
Installing ri documentation for systemu-2.6.4
Parsing documentation for uuidtools-2.1.5
Installing ri documentation for uuidtools-2.1.5
Parsing documentation for wmi-lite-1.0.0
Installing ri documentation for wmi-lite-1.0.0
Done installing documentation for builder, chef, chef-zero, coderay, diff-lcs, erubis, ffi, ffi-yajl, hashie, highline, ipaddress, knife-solo, libyajl2, method_source, mime-types, mixlib-authentication, mixlib-cli, mixlib-config, mixlib-log, mixlib-shellout, multi_json, net-dhcp, net-scp, net-ssh, net-ssh-gateway, net-ssh-multi, ohai, plist, pry, rack, rspec, rspec-core, rspec-expectations, rspec-its, rspec-mocks, rspec-support, rspec_junit_formatter, serverspec, slop, specinfra, systemu, uuidtools, wmi-lite after 51 seconds
43 gems installed
vagrant@chef-book:~/cookbooks$
```
You can already leverage `knife solo` with the cookbook you created. Lets do that now. NOTE: this is a tad bit tricky with virtualbox, so bear with me.

There are three/four main commands with `knife solo`.

`knife solo init mychefrepo`: it creates a directory structure that `knife solo` needs to be able to upload and run your cookbooks. example:

```
mychefrepo/
├── .chef
│   └── knife.rb
├── cookbooks
├── data_bags
├── nodes
├── roles
└── site-cookbooks
```

`knife solo prepare root@hostname`: which checks for the version of chef-solo on the box, and if it's too low installs the newest.

`knife solo cook root@hostname`: runs the cookbooks that you put in the `nodes/hostname.json` `run_list` which is just like the `run_list` like the `core.json` from earlier.

`knife solo bootstrap root@hostname`: combines the prepare and cook into one command. In theory you can have a `node/hostname.json` set up and run one command and provision a box exactly how you want. Pretty neat eh?

So, lets actually do it.  Go to your `~/` and create a `knife_solo` directory you can play out of.

```bash
vagrant@chef-book:~/cookbooks$ cd ~
vagrant@chef-book:~$ mkdir knife_solo
vagrant@chef-book:~$ cd knife_solo/
vagrant@chef-book:~/knife_solo$
```

Now type the `knife solo init .` command to make it `knife solo`ized. 

```bash
vagrant@chef-book:~/knife_solo$ knife solo init .
Creating kitchen...
Creating knife.rb in kitchen...
Creating cupboards...
vagrant@chef-book:~/knife_solo$
```

Change directory into the `site-cookbook` directory.

I'd like to take a quick moment to talk about the _two_ directories that are here. `cookbooks/` and `site-cookbooks/`, the `cookbook/` directory is for [berkshelf](http://berkshelf.com/) or [librarian](https://github.com/applicationsonline/librarian-chef) to put cookbooks before uploading them to your provisioning box. So remember that, `site-cookbooks/` is the place to put _your_ cookbooks that you want to upload. (If you do put them in `cookbooks/` it'll delete them after the run _and_ can't find them either, so it's a waste, don't bother.)

Let's copy the cookbook we created earlier as the root user:
```bash
vagrant@chef-book:~/knife_solo$ cd site-cookbooks/
vagrant@chef-book:~/knife_solo/site-cookbooks$ sudo cp -rv /root/core/cookbooks/base/ ./
'/root/core/cookbooks/base/' -> './base'
'/root/core/cookbooks/base/files' -> './base/files'
'/root/core/cookbooks/base/files/default' -> './base/files/default'
'/root/core/cookbooks/base/files/default/deployer_key.pub' -> './base/files/default/deployer_key.pub'
'/root/core/cookbooks/base/files/default/ssh_config' -> './base/files/default/ssh_config'
'/root/core/cookbooks/base/recipes' -> './base/recipes'
'/root/core/cookbooks/base/recipes/ssh.rb' -> './base/recipes/ssh.rb'
'/root/core/cookbooks/base/recipes/deployer.rb' -> './base/recipes/deployer.rb'
'/root/core/cookbooks/base/recipes/default.rb' -> './base/recipes/default.rb'
vagrant@chef-book:~/knife_solo/site-cookbooks$ sudo chown -R vagrant.vagrant base/
vagrant@chef-book:~/knife_solo/site-cookbooks$
```

Great, now go up a directory to the `nodes/` directory, and create a file called `localhost.json`

```bash
vagrant@chef-book:~/knife_solo/site-cookbooks$ cd ../nodes/
vagrant@chef-book:~/knife_solo/nodes$ vim localhost.json
```

Add the save `core.json` file that you had from the chef-solo section.

```json
{
    "run_list": [ "recipe[base::default]","recipe[base::ssh]","recipe[base::deployer]" ]
}
```

Just because I want to make sure this goes off without a hitch, we're going to copy the root user's public key into it's `.ssh/authorized_keys` file and copy it's private key to our `.ssh` directory. Yes, I realize that's confusing, but read the following commands to make sense of it:

```bash
vagrant@chef-book:~/knife_solo/nodes$ sudo cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys
vagrant@chef-book:~/knife_solo/nodes$ cd ~/.ssh
vagrant@chef-book:~/.ssh$
vagrant@chef-book:~/.ssh$ sudo cp /root/.ssh/id_rsa /root/.ssh/id_rsa.pub .
vagrant@chef-book:~/.ssh$ sudo chown vagrant.vagrant *
```

Let's add the root user's private key to our key chain and try SSH:

```bash
vagrant@chef-book:~/.ssh$ ssh-add id_rsa
Identity added: id_rsa (id_rsa)
vagrant@chef-book:~/.ssh$ ssh root@localhost
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is 2c:03:a9:7b:5b:8f:e3:19:e4:08:46:4b:f8:41:1d:18.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
root@chef-book:~# exit
logout
Connection to localhost closed.
vagrant@chef-book:~/.ssh$
```

If that works, we're ready to start playing with `knife solo`.

Hop back to `~/knife_solo`.

```base
vagrant@chef-book:~/.ssh$ cd ~/knife_solo
vagrant@chef-book:~/knife_solo$
```

Lets start with a `knife solo prepare` just to see it work:

```bash
vagrant@chef-book:~/knife_solo$ knife solo prepare root@localhost
Bootstrapping Chef...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 18378  100 18378    0     0   2963      0  0:00:06  0:00:06 --:--:--  3931
Downloading Chef 12.1.1 for ubuntu...
downloading https://www.opscode.com/chef/metadata?v=12.1.1&prerelease=false&nightlies=false&p=ubuntu&pv=14.04&m=x86_64
  to file /tmp/install.sh.5302/metadata.txt
trying wget...
url	https://opscode-omnibus-packages.s3.amazonaws.com/ubuntu/13.04/x86_64/chef_12.1.1-1_amd64.deb
md5	028e2335ff309fa4555ed0dfd486162f
sha256	108bc8a1786b1a4cfbb42dcea0e4990221340aae669878df3f67fffe1fb1ad24
downloaded metadata file looks valid...
downloading https://opscode-omnibus-packages.s3.amazonaws.com/ubuntu/13.04/x86_64/chef_12.1.1-1_amd64.deb
  to file /tmp/install.sh.5302/chef_12.1.1-1_amd64.deb
trying wget...
Comparing checksum with sha256sum...
Installing Chef 12.1.1
installing with dpkg...
(Reading database ... 123538 files and directories currently installed.)
Preparing to unpack .../chef_12.1.1-1_amd64.deb ...
Unpacking chef (12.1.1-1) over (12.1.1-1) ...
Setting up chef (12.1.1-1) ...
Thank you for installing Chef!
vagrant@chef-book:~/knife_solo$
```

As you can see it does check the version of chef, then download and install the newsest for you.

Now lets go on to `knife solo cook`:

```bash
vagrant@chef-book:~/knife_solo$ knife solo cook root@localhost
Running Chef on localhost...
Checking Chef version...
Uploading the kitchen...
Generating solo config...
Running Chef...
Starting Chef Client, version 12.1.1
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
  * group[deployer] action create (up to date)
  * user[deployer] action create (up to date)
  * directory[/home/deployer/.ssh] action create (up to date)
  * cookbook_file[/home/deployer/.ssh/authorized_keys] action create_if_missing (up to date)

Running handlers:
Running handlers complete
Chef Client finished, 0/11 resources updated in 7.748576421 seconds
vagrant@chef-book:~/knife_solo$
```

CHA-CHING! Awesome. Bonus Round: use `knife solo bootstrap`.

Now image this with a "cloud" box that you have root access to.  You just provisioned a box with recipes you wrote.

## knife rackspace/knife ec2

[knife rackspace](https://github.com/opscode/knife-rackspace) and [knife ec2](https://github.com/opscode/knife-ec2) are front ends to talk to the respected cloud providers.  They both install with a gem:

### knife rackspace

```bash
vagrant@chef-book:~/knife_solo$ sudo gem install knife-rackspace
Fetching: little-plugger-1.1.3.gem (100%)
Successfully installed little-plugger-1.1.3
Fetching: logging-1.8.2.gem (100%)
Successfully installed logging-1.8.2
Fetching: nori-1.1.5.gem (100%)
Successfully installed nori-1.1.5
Fetching: httpi-0.9.7.gem (100%)
Successfully installed httpi-0.9.7
Fetching: mini_portile-0.6.2.gem (100%)
Successfully installed mini_portile-0.6.2

[-- snip --]

Parsing documentation for winrm-1.2.0
Installing ri documentation for winrm-1.2.0
Parsing documentation for winrm-s-0.2.4
Installing ri documentation for winrm-s-0.2.4
Done installing documentation for CFPropertyList, akami, em-winrm, eventmachine, excon, fission, fog, fog-atmos, fog-aws, fog-brightbox, fog-core, fog-ecloud, fog-json, fog-profitbricks, fog-radosgw, fog-riakcs, fog-sakuracloud, fog-serverlove, fog-softlayer, fog-storm_on_demand, fog-terremark, fog-vmfusion, fog-voxel, fog-xml, formatador, gssapi, gyoku, httpclient, httpi, inflecto, knife-rackspace, knife-windows, little-plugger, logging, mini_portile, nokogiri, nori, rubyntlm, savon, wasabi, winrm, winrm-s after 118 seconds
42 gems installed
vagrant@chef-book:~/knife_solo$
```

There is a nasty catch with knife-rackspace - you can't have multiple versions of the gem installed so you need to run the following to confirm you only have one version installed (and how to remove older version):

```bash
vagrant@chef-book:~/knife_solo$ gem list --local | grep knife-rackspace
knife-rackspace (0.6.2, 0.5.12)
vagrant@chef-book:~/knife_solo$ sudo gem uninstall knife-rackspace -v "= 0.5.12"
Successfully uninstalled knife-rackspace-0.5.12
vagrant@chef-book:~/knife_solo$ gem list --local | grep knife-rackspace
knife-rackspace (0.6.2)
```

Keep this in mind.

After installing it, you need to add this to your `knife.rb` file:

```ruby
knife[:rackspace_api_username] = "Your Rackspace API username"
knife[:rackspace_api_key] = "Your Rackspace API Key"
```

Run `knife rackspace server list` and you should see your cloud machines.

Go ahead and attempt to spin up a box:

```bash
vagrant@chef-book:~/knife_solo$ knife rackspace server create --server-name test -f 4
Instance ID: 17c3d362-6930-452f-86f4-7f4ce0a9453e
Name: test
Flavor: 2GB Standard Instance
Image: Ubuntu 10.04 LTS (Lucid Lynx)
Metadata: []
RackConnect Wait: no
ServiceLevel Wait: no
...............................................................................................Metadata: []

Waiting server.
Public DNS Name: 192.43.66.2.static.cloud-ips.com
Public IP Address: 192.43.66.2
Private IP Address: 10.208.154.177
Password: aoeu534234
Metadata: []

Waiting for sshddone
Bootstrapping Chef on 192.43.66.2
192.43.66.2 --2013-10-25 21:58:07--  https://www.opscode.com/chef/install.sh
192.43.66.2 Resolving www.opscode.com... 184.106.28.83
192.43.66.2 Connecting to www.opscode.com|184.106.28.83|:443...
192.43.66.2 connected.
192.43.66.2 HTTP request sent, awaiting response... 200 OK
192.43.66.2 Length: 6790 (6.6K) [application/x-sh]
192.43.66.2 Saving to: `STDOUT'
192.43.66.2
 0% [                                       ] 0           --.-K/s
 100%[======================================>] 6,790       --.-K/s   in 0s
 192.43.66.2
 192.43.66.2 2013-10-25 21:58:07 (778 MB/s) - written to stdout [6790/6790]
 192.43.66.2
 192.43.66.2 Downloading Chef 11.6.2 for ubuntu...
 192.43.66.2 Installing Chef 11.6.2
 192.43.66.2 Selecting previously deselected package chef.
 192.43.66.2 (Reading database ...
 (Reading database ... 70%
 (Reading database ... 75%
 (Reading database ... 85%
 (Reading database ... 90%
 (Reading database ... 95%
 (Reading database ... 15859 files and directories currently installed.)
 192.43.66.2 Unpacking chef (from .../chef_11.6.2_amd64.deb) ...
 192.43.66.2 Setting up chef (11.6.2-1.ubuntu.10.04) ...
 192.43.66.2 Thank you for installing Chef!
 192.43.66.2
 192.43.66.2 Starting Chef Client, version 11.6.2
 192.43.66.2 Creating a new client identity for test using the validator key.
 192.43.66.2 resolving cookbooks for run list: []
 192.43.66.2 Synchronizing Cookbooks:
 192.43.66.2 Compiling Cookbooks...
 192.43.66.2 [2013-10-25T21:58:20+00:00] WARN: Node test has an empty run list.
 192.43.66.2 Converging 0 resources
 192.43.66.2 Chef Client finished, 0 resources updated

 Instance ID: 17c3d362-6930-452f-86f4-7f4ce0a9453e
 Host ID: a8db57a86005a2d4b96f63ccb3ac4a0a67fe284ba1928e4eb39ea1a3
 Name: test
 Flavor: 2GB Standard Instance
 Image: Ubuntu 10.04 LTS (Lucid Lynx)
 Metadata: []
 Public DNS Name: 192.43.66.2.static.cloud-ips.com
 Public IP Address: 192.43.66.2
 Private IP Address: 10.208.154.177
 Password: aoeu534234
 Environment: _default
vagrant@chef-book:~/knife_solo$
```

Bonus Round: Try running the `knife rackspace server list` and maybe `knife solo bootstrap` would work here? 

### knife ec2

`knife ec2` is pretty much the same game, you can install it via the gem. We have already installed knife-rackspace on the chef-book vm and they have a lot of the same dependancies, so there aren't many gems to fetch.

```bash
vagrant@chef-book:~/knife_solo$ sudo gem install knife-ec2
Fetching: knife-ec2-0.10.0.gem (100%)
Successfully installed knife-ec2-0.10.0
Parsing documentation for knife-ec2-0.10.0
Installing ri documentation for knife-ec2-0.10.0
Done installing documentation for knife-ec2 after 0 seconds
1 gem installed
vagrant@chef-book:~/knife_solo$
```

You'll need to edit your `knife.rb` file and add these lines to it:

```ruby
knife[:aws_access_key_id] = "Your AWS Access Key ID"
knife[:aws_secret_access_key] = "Your AWS Secret Access Key"
```

You have more or less the same tools as `knife rackspace` but here's an example:

```bash
vagrant@chef-book:~/knife_solo$ knife ec2 server create -I ami-7000f019 -f m1.small
```

There are a ton more options, but this will get you started. 

## knife spork

When it comes to knife spork, it's a workflow tool instead of a API tool. I wrote a [blog](http://jjasghar.github.io/blog/2013/10/04/moving-from-one-chef-to-multiple-chefs/) post about it, but I'll rip out a good portion of it and put it here.  I should mention this plugin is in relation to the chef-server, so if this doesn't make sense yet, don't worry, the next section is going start on chef-server.

Obviously the first thing you need to do is install it. Luckily it's a gem so you can just do the following. If you read the docs there are a bunch of places that you `.yml` gets read from, but I chose this because I like having all my chef stuff in `.chef` so I don't have to think about pulling anything other than `.chef` if I want to move boxes.

```bash
vagrant@chef-book:~/knife_solo$ sudo gem install knife-spork
Fetching: git-1.2.9.1.gem (100%)
Successfully installed git-1.2.9.1
Fetching: app_conf-0.4.2.gem (100%)
Successfully installed app_conf-0.4.2
Fetching: diffy-3.0.7.gem (100%)
Successfully installed diffy-3.0.7
Fetching: knife-spork-1.5.1.gem (100%)
Successfully installed knife-spork-1.5.1
Parsing documentation for app_conf-0.4.2
Installing ri documentation for app_conf-0.4.2
Parsing documentation for diffy-3.0.7
Installing ri documentation for diffy-3.0.7
Parsing documentation for git-1.2.9.1
Installing ri documentation for git-1.2.9.1
Parsing documentation for knife-spork-1.5.1
Installing ri documentation for knife-spork-1.5.1
Done installing documentation for app_conf, diffy, git, knife-spork after 1 seconds
4 gems installed
vagrant@chef-book:~/knife_solo$ touch ~/.chef/spork-config.yml
vagrant@chef-book:~/knife_solo$
```

After installing the gem and touching the file, you can run `knife spork info`, it should say everything is disabled.  If so, then you are read to create the config file.

The example [config](https://raw.github.com/jonlives/knife-spork/master/README.md) is on the main site, but I copied the demo one here too.

```yaml
default_environments:
  - development
  - production
environment_groups:
  qa_group:
    - quality_assurance
    - staging
  test_group:
    - user_testing
    - acceptance_testing
version_change_threshold: 2
environment_path: "/home/me/environments"
plugins:
  campfire:
    account: myaccount
    token: a1b2c3d4...
  hipchat:
    api_token: ABC123
    rooms:
      - General
      - Web Operations
    notify: true
    color: yellow
  jabber:
    username: YOURUSER
    password: YOURPASSWORD
    nickname: Chef Bot
    server_name: your.jabberserver.com
    server_port: 5222
    rooms:
      - engineering@your.conference.com/spork
      - systems@your.conference.com/spork
  git:
    enabled: true
  irccat:
    server: irccat.mydomain.com
    port: 12345
    gist: "/usr/bin/gist"
    channel: ["chef-annoucements"]
  graphite:
    server: graphite.mydomain.com
    port: 2003
  eventinator:
    url: http://eventinator.mydomain.com/events/oneshot
```

All in all this seems pretty self [explanatory](https://github.com/jonlives/knife-spork#default-environments) but the most important things to change are `environment_path` and disabling the plugins (by removing them) here.  For my company I only used the git plugin and...well that was it. :)

By the way there are only a few plugins, [here](https://github.com/jonlives/knife-spork/tree/master/plugins) is a link to the different .md files on each.

Ok, so you have everything set up, what do you do now?

Usage
-----

The first step is to run `knife spork check COOKBOOK --all` where COOKBOOK is one of your commonly updated/tweaked cookbooks.  Spork checks against what you have locally compared to what's in the server, here's an example:

```bash
Checking versions for cookbook nagios...

Local Version:
  5.1.5

Remote Versions: (* indicates frozen)
  5.1.5
  5.1.4
  5.1.3
  5.1.2

ERROR: The version 5.1.5 exists on the server and is not frozen. Uploading will overwrite!
```

As you can see with the error, it's pretty self explaintory.

The second step is to bump the version with `knife spork bump nagios patch`, here's some example output:

```bash
Git: Pulling latest changes from /Users/jasghar/repo/chef_repo/environments
Pulling latest changes from git submodules (if any)
Git: Pulling latest changes from /Users/jasghar/repo/chef_repo/cookbooks/nagios
Pulling latest changes from git submodules (if any)
Successfully bumped nagios to v5.1.6!
```

Now as you can see I have the git plugin working, and it without thinking about it, updates the metadata.rb so you don't have to. (I HATE that part of chef, I always forget.) Now you can go off make your changes.

Now lets start talking about [chef-server](10-opensource-vs-hosted-chefserver.md)!
