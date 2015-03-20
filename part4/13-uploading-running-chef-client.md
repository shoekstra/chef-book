# Uploading your cookbook to your chef server

Before we try to upload our cookbook, I need you to run something like this:

```bash
vagrant@chef-book:~$ knife status
0 minutes ago, chef-book, chef-book, 10.0.2.15, ubuntu 14.04.
vagrant@chef-book:~$
```

If `knife` can talk to your chef server you are good to go. If not, [hop back](../README.md#contents) a couple sections and try to figure out what broke. Don't worry I'll be here when you get back.

Perfect, lets start off by uploading our base cookbook. Go ahead and look at your `knife.rb` for me:

```bash
vagrant@chef-book:~$ cat .chef/knife.rb
log_level                :info
log_location             STDOUT
node_name                'admin'
client_key     '~/.chef/admin.pem'
chef_server_url          'https://ec2-23-20-27-29.compute-1.amazonaws.com'
syntax_check_cache_path  '/root/.chef/syntax_check_cache'
vagrant@chef-book:~$
```

If you notice I don't have a `cookbook_path` directory, I need to add one:

```bash
vagrant@chef-book:~$ cat .chef/knife.rb
log_level                :info
log_location             STDOUT
node_name                'admin'
client_key     '~/.chef/admin.pem'
cookbook_path            ["~/cookbooks"]
chef_server_url          'https://awesome-chef-server.domain.com'
syntax_check_cache_path  '/root/.chef/syntax_check_cache'
vagrant@chef-book:~$
```

Pretty self explaintory eh? 

Lets try and upload the `base` cookbook:

```bash
vagrant@chef-book:~$ knife cookbook upload base
ERROR: Errno::ENOENT: No such file or directory @ rb_sysopen - /home/vagrant/cookbooks/base/README.md
vagrant@chef-book:~$
```

Do'h! It seems we need a `README.md` now, this is great habit to have. I'm lazy so I'll just touch the file and try again:

```bash
vagrant@chef-book:~$ touch /home/vagrant/cookbooks/base/README.md
vagrant@chef-book:~$
vagrant@chef-book:~$ knife cookbook upload base
Uploading base         [0.1.0]
Uploaded 1 cookbook.
vagrant@chef-book:~$
```

Awesome! Notice the `[0.1.0]` that is controlled about the [metadata.rb](http://docs.opscode.com/essentials_cookbook_metadata.html) file. Now check out your cookbooks on your server and you should also see `[0.1.0]`.

Now let's add base to the `run_list` for the `chef-book` node:

```bash
vagrant@chef-book:~$ knife node run_list add chef-book base
chef-book:
  run_list: recipe[base]
vagrant@chef-book:~$
```

Nice! That looks a tad bit fimilar right? Go ahead and run `chef-client` now:

```bash
vagrant@chef-book:~$ sudo chef-client
[2015-03-19T06:15:08-05:00] INFO: Forking chef instance to converge...
Starting Chef Client, version 12.1.1
[2015-03-19T06:15:08-05:00] INFO: *** Chef 12.1.1 ***
[2015-03-19T06:15:08-05:00] INFO: Chef-client pid: 11852
[2015-03-19T06:15:13-05:00] INFO: Run List is [recipe[base]]
[2015-03-19T06:15:13-05:00] INFO: Run List expands to [base]
[2015-03-19T06:15:13-05:00] INFO: Starting Chef Run for chef-book
[2015-03-19T06:15:13-05:00] INFO: Running start handlers
[2015-03-19T06:15:13-05:00] INFO: Start handlers complete.
resolving cookbooks for run list: ["base"]
[2015-03-19T06:15:15-05:00] INFO: Loading cookbooks [base@0.1.0]
Synchronizing Cookbooks:
[2015-03-19T06:15:15-05:00] INFO: Storing updated cookbooks/base/recipes/deployer.rb in the cache.
[2015-03-19T06:15:15-05:00] INFO: Storing updated cookbooks/base/recipes/default.rb in the cache.
[2015-03-19T06:15:15-05:00] INFO: Storing updated cookbooks/base/files/default/deployer_key.pub in the cache.
[2015-03-19T06:15:15-05:00] INFO: Storing updated cookbooks/base/README.md in the cache.
[2015-03-19T06:15:15-05:00] INFO: Storing updated cookbooks/base/metadata.rb in the cache.
[2015-03-19T06:15:17-05:00] INFO: Storing updated cookbooks/base/files/default/ssh_config in the cache.
[2015-03-19T06:15:18-05:00] INFO: Storing updated cookbooks/base/recipes/ssh.rb in the cache.
  - base
Compiling Cookbooks...
Converging 6 resources
Recipe: base::default
  * apt_package[vim] action install[2015-03-19T06:15:18-05:00] INFO: Processing apt_package[vim] action install (base::default line 2)
 (up to date)
  * apt_package[ntp] action install[2015-03-19T06:15:19-05:00] INFO: Processing apt_package[ntp] action install (base::default line 2)
 (up to date)
  * apt_package[build-essential] action install[2015-03-19T06:15:19-05:00] INFO: Processing apt_package[build-essential] action install (base::default line 2)
 (up to date)
Recipe: base::ssh
  * apt_package[openssh-server] action install[2015-03-19T06:15:19-05:00] INFO: Processing apt_package[openssh-server] action install (base::ssh line 1)
 (up to date)
  * cookbook_file[/etc/ssh/ssh_config] action create[2015-03-19T06:15:19-05:00] INFO: Processing cookbook_file[/etc/ssh/ssh_config] action create (base::ssh line 5)
 (up to date)
  * service[ssh] action enable[2015-03-19T06:15:19-05:00] INFO: Processing service[ssh] action enable (base::ssh line 13)
 (up to date)
  * service[ssh] action start[2015-03-19T06:15:19-05:00] INFO: Processing service[ssh] action start (base::ssh line 13)
 (up to date)
[2015-03-19T06:15:19-05:00] INFO: Chef Run complete in 6.529510641 seconds

Running handlers:
[2015-03-19T06:15:19-05:00] INFO: Running report handlers
Running handlers complete
[2015-03-19T06:15:19-05:00] INFO: Report handlers complete
Chef Client finished, 0/7 resources updated in 11.11435179 seconds
[2015-03-19T06:15:19-05:00] INFO: Sending resource update report (run-id: a83e2e52-ec97-47bb-b0cd-cf2afc02a439)
vagrant@chef-book:~$
```

CHA-CHING!!!! Congrats you have uploaded your cookbook and run the client to apply it. Awesome!

Lets take a step back from chef and talk about versioning. The chef community has basiclly decided that [Semantic Versioning](http://semver.org/) is the defacto standard for cookbooks. There is A TON of data on that link, but to sum it up I explain it like this:
> A patch change is "x.y.Z". Anything larger than a patch, example adding a test, is a "x.Y.z" change. Anything that adds new functionality for an internal cookbook is "X.y.z," if you are pulling public run cookbooks stick with the system they use.

It's not 100% accurate, but it's a great rule of thumb.

Ok, so why should I care about versioning, well it's good to track your changes right? It's also a great way "pin" versions for different environments, which I'll talk about [next](14-environments-roles-oh-my.md).
