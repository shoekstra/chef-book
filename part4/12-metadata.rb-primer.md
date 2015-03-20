# metadata.rb

So you have a working chef server eh? Great! We're almost ready to start uploading cookbooks. :)

Before we can upload cookbooks, we need to cover the [metadata](http://docs.opscode.com/essentials_cookbook_metadata.html).rb file and what it does.

The metadata.rb is the core file that gives extemely useful data about the cookbook. The metadata.rb controls the version number like above, and a lot other basic generic data. If you recall back to the [knife.rb](../part3/08-knife.md#kniferb) section where you created the cookbook, `new_cookbook/metadata.rb` was created. If you have blown it away, this is what it looks like:

```ruby
name             'new_cookbook'
maintainer       'YOUR_COMPANY_NAME'
maintainer_email 'YOUR_EMAIL'
license          'All rights reserved'
description      'Installs/Configures new_cookbook'
long_description IO.read(File.join(File.dirname(__FILE__), 'README.md'))
version          '0.1.0'
```

Pretty straight forward eh? 

Remember that cookbook you created? Way back in [Part 2-6](../part2/06-write-simple-base-cookbook.md)? We're gonna get that one preped for upload.  

Let's copy it to our `~/cookbooks` directory:

```bash
vagrant@chef-book:~$ cp -r /vagrant/cookbooks/base/ cookbooks/
vagrant@chef-book:~$
```

Now because we didn't create a `metadata.rb` in your base cookbook, you can't control your version number. Lets do that now:

```ruby
name             'base'
maintainer       'JJ Asghar'
maintainer_email 'jj.asghar@peopleadmin.com'
license          'All rights reserved'
description      'Installs/Configures base'
long_description IO.read(File.join(File.dirname(__FILE__), 'README.md'))
version          '0.1.0'
```

As of chef 11.8, the `name` is required for converges to complete. I've searched high and low for the ticket that's associated with this; but here's the general [release notes](http://docs.opscode.com/release_notes.html).  Luckily the error is extremely obvious if you don't have have `name`, and the fix is to add it. ;)

That wraps up the metadata.rb primer, we'll upload our base cookbook in the [next section](13-uploading-running-chef-client.md).
