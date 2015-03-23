# Places to find cookbooks

So you can write your own cookbook now, you can make some config changes if you want to, but man that's a ton of work right?  Luckily the chef community has a huge library of chef cookbooks out there that you can take from and let the community QA them instead of yourself.

The first place is [here](http://community.opscode.com/). It's the "offical" community site for opscode and has a great _stable_ selection of cookbooks for general purpose usage. [mysql](http://community.opscode.com/cookbooks/mysql), [apache2](http://community.opscode.com/cookbooks/apache2), [nginx](http://community.opscode.com/cookbooks/nginx) are tried and true, I've used them in multiple production environments with little to no issues. Keep in mind though these community cookbooks are extremely more complex then we have been playing with, so I would read all the documentation that comes with them and test them out as much as you can on your Vagrant setup.

Next is straight [github](http://github.com). If you noticed on most of the community cookbook are actually hosted directly on github, hell the "main" [opscode-cookbooks](https://github.com/opscode-cookbooks/) are hosted there. If you go to the [search](https://github.com/search) and look for [chef cookbook](https://github.com/search?q=chef+cookbook&type=Repositories&ref=searchresults), you'll find a TON. On the other hand though these are useally not as stable, so use at your own risk. Also don't forget READ THE DOCS.

After you've found a few, and you've pulled them down, made the local changes you wanted to, uploaded them to your chef server, you should seriously consider looking into [chef_repo](https://github.com/opscode/chef-repo). This will force you to start tracking you changes in a git repo and it's the "proper way". To quote the repo:
> Every Chef installation needs a Chef Repository. This is the place where cookbooks, roles, config files and other artifacts for managing systems with Chef will live. We strongly recommend storing this repository in a version control system such as Git and treat it like source code.

* Note: although cloning this repository is now deprecated in favour of using `chef generate repo`, it contains some handy README files in each directory that describe the directory's purpose and is worth checking out.

There'll be some logistical changes you'll need to make with your `knife.rb` converting to this repo, but it makes you commit your changes and it's a great way to back it up. Congrats, you now have a chef_repo, you can push community cookbooks and run them, what's next? [Berkshelf](../part5/16-berkshelf-primer.md) is the apt-get to chef as pulling down community cookbooks is to building from source.
