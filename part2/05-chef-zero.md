chef-zero
---------

So we have a working _disposable_ vm with with Vagrant now?

Great, let's actually start playing with Chef.

We're going to start with [chef-zero][cz] (or chef-client that uses a local server),
which, believe it or not, can cover 75-85% of the use cases of Chef.
Keep this in mind as you go through this book, because if it fits there may be
no need to go farther.

Since we installed the chef-dk during our vm-setup, we already have the
chef-client with chef-zero.

Restart your Vagrant box with `vagrant up` if you have stopped or destroyed it.
Type these following commands in to confirm we are set up correctly.

Let's run chef-zero:

```bash
root@chef-book:~# chef-client -z
[2015-03-10T10:00:13-05:00] WARN: No config file found or specified on command line, using command line options.
[2015-03-10T10:00:13-05:00] WARN: No cookbooks directory found at or above current directory.  Assuming /root.
Starting Chef Client, version 12.0.3
resolving cookbooks for run list: []
Synchronizing Cookbooks:
Compiling Cookbooks...
[2015-03-10T10:00:19-05:00] WARN: Node chef-book has an empty run list.
Converging 0 resources

Running handlers:
Running handlers complete
Chef Client finished, 0/0 resources updated in 6.157297261 seconds
root@chef-book:~#
```

You will see few warnings at first, but other than that... nothing really happened.

Chef, uses a compiled top down set of instructions, or _recipes_,
to build a machine how
you want it to be. You can see that this process starts with
the `Compiling Cookbooks...` line.  But there's nothing to compile.

You can fix the first warning by doing:

```bash
root@chef-book:~# mkdir -p ~/.chef && touch ~/.chef/knife.rb
root@chef-book:~#
```

Now let's go on to actually making Chef do something!

Move on to [A simple cookbook](06-write-simple-base-cookbook.md)

[cz]: http://www.getchef.com/blog/2013/10/31/chef-client-z-from-zero-to-chef-in-8-5-seconds/
