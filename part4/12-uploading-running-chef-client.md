# Uploading your cookbook to your chef server

So you have a working chef server eh? Great! Now let's actually start working with it.  Before you start anything, I need you to run something like this:

```bash
agrant@chef-book:~$ knife status
0 minutes ago, chef-book, chef-book, 10.0.2.15, ubuntu 14.04.
agrant@chef-book:~$
```

If `knife` can talk to your chef server you are good to go. If not, [hop back](../README.md#contents) a couple sections and try to figure out what broke. Don't worry I'll be here when you get back.

Perfect, lets start off.

Remember that cookbook you created? Way back in [Part 2-6](../part2/06-write-simple-base-cookbook.md) we're gonna upload that one first off. Go ahead and look at your `knife.rb` for me:

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

If you notice I don't have a `cookbook_path` directory, i need add one:

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

Pretty self explaintory eh? Go ahead and copy that `base/` cookbook into the directory that you declare on that line:

```bash
vagrant@chef-book:~$ cp -r /vagrant/cookbooks/base/ cookbooks/
vagrant@chef-book:~$
```

Lets try and upload the cookbook:

```bash
vagrant@chef-book:~$ knife cookbook upload base
Uploading base         [0.1.0]
Uploaded 1 cookbook.
vagrant@chef-book:~$
```

Awesome! You uploaded your first cookbook. Notice the `[0.1.0]` that is controlled about the [metadata.rb](http://docs.opscode.com/essentials_cookbook_metadata.html) file. We'll talk about that is a sec.

Now let's add base to the `run_list`:

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

Lets talk about that [metadata.rb](13-metadata.rb-primer.md).
