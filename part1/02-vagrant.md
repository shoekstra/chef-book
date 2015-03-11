Vagrant
-------

Vagrant is a great tool designed for developers to create disposable machines with a quick turn around.  In our case we want to install the binary NOT the gem, so go [here to download](http://www.vagrantup.com/downloads.html) (or use your native package manager).

After you installed it, go ahead and spin up [Terminal|iTerm2|xterm|cmd] and type `vagrant`. You should see something like the following:

```bash
[~] % vagrant
Usage: vagrant [options] <command> [<args>]

    -v, --version                    Print the version and exit.
    -h, --help                       Print this help.

Common commands:
     box             manages boxes: installation, removal, etc.
     connect         connect to a remotely shared Vagrant environment
     destroy         stops and deletes all traces of the vagrant machine
     global-status   outputs status Vagrant environments for this user
     halt            stops the vagrant machine
     help            shows the help for a subcommand
     init            initializes a new Vagrant environment by creating a Vagrantfile
     login           log in to HashiCorp's Atlas
     package         packages a running vagrant environment into a box
     plugin          manages plugins: install, uninstall, update, etc.
     provision       provisions the vagrant machine
     push            deploys code in this environment to a configured destination
     rdp             connects to machine via RDP
     reload          restarts vagrant machine, loads new Vagrantfile configuration
     resume          resume a suspended vagrant machine
     share           share your Vagrant environment with anyone in the world
     ssh             connects to machine via SSH
     ssh-config      outputs OpenSSH valid configuration to connect to the machine
     status          outputs status of the vagrant machine
     suspend         suspends the machine
     up              starts and provisions the vagrant environment
     version         prints current and latest Vagrant version

For help on any individual command run `vagrant COMMAND -h`

Additional subcommands are available, but are either more advanced
or not commonly used. To see all subcommands, run the command
`vagrant list-commands`.
```

If you see this, you are ready for the next step.

I personally like creating a `vagrant/` directory, and place a directory inside each of them for different VMs. The first test to make sure you have everything working is this.

```bash
[~] % cd ~
[~] % mkdir -p vagrant/trusty64
[~] % cd vagrant/trusty64
[~/vagrant/trusty64] % vagrant init ubuntu/trusty64
[~/vagrant/trusty64] % vagrant up
```

Then you should see something that resembles this:

```bash
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'ubuntu/trusty64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ubuntu/trusty64' is up to date...
==> default: Setting the name of the VM: trusty64_default_1426077947313_38622
==> default: Clearing any previously set forwarded ports...
==> default: Fixed port collision for 22 => 2222. Now on port 2200.
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 => 2200 (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2200
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection timeout. Retrying...
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if its present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /vagrant => ~/vagrant/trusty64
```

Go ahead and `vagrant ssh` into the box and you'll see that you have a complete box that can run Linux commands, basically anything. Type something like `sudo apt-get update && sudo apt-get install vim -y`. You'll see vim being installed. Start it up with `vim test-file` and write some things in there. `wq` out of it. Go ahead and `logout` of the machine. You should see your original command prompt from your host machine now. Type `vagrant ssh` and you should see the file that you wrote out, `cat test-file`.

As you can see you have been able to create a base box, ssh into it, change it around, and log out then log back in without losing any of your data.
The next step is to destroy it and start over, so in the place you did your `vagrant up`, type `vagrant destroy -f`. This is a "Do not pass Go" type of destroy, so be careful, you have been warned.
When you run `vagrant destroy -f`, you'll see output like:

```bash
[~/vagrant/trusty64] % vagrant destroy -f
[default] Forcing shutdown of VM...
[default] Destroying VM and associated drives...
[~/vagrant/trusty64] %
```

It shuts down the machine, and blows it up. If you attempt to `vagrant ssh` it'll say:

```bash
[~/vagrant/trusty64] % vagrant ssh
VM must be created before running this command. Run `vagrant up` first.
[~/vagrant/trusty64] %
```

Because the machine is gone. You can simply run `vagrant up` to re-create it. You'll notice that neither the `test-file` nor vim is there anymore, and that's expected, you blew it up didn't you? I strongly suggest playing around with `vagrant`. The [docs](http://docs.vagrantup.com/v2/) for version 2 are extremely straight forward, and you should spend the time to get comfortable with it. It'll make your chef experience so much better.

Bonus round: Try to set up an Apache server/nginx server and use [port forwarding](http://docs.vagrantup.com/v2/networking/forwarded_ports.html) with Vagrant so you can hit http://localhost:8080 in your browser and see the default page being served by the VM.

Move on to [The beginning of your playground](03-vm-setup.md)
