# Vagrant

https://developer.hashicorp.com/vagrant

## Content

[Install](#install)

[Comands](#comands)

[Vagrant help](#vagrant-help)

[Lookup PATH](#lookup-path)

[Versions](#versions)

[Boxes](#boxes)

[Version Control](#version-control)

[Sharing folders](#sharing-folders)

[Network](#network)

[Teardown](#teardown)

[Provisioning and Provisioners](#provisioning-and-provisioners)

[Private Networking](#private-networking)

[Public networking](#public-networking)

[Boxes](#boxes)

### Install

https://developer.hashicorp.com/vagrant/docs/installation


required virtualbox or VMware or AWS EC2

#### Windows

Download virtual box and install
Download from http://vagrant.com installer -> (C:\vargant) 


[Content](#content)


### Comands

`vagrant --version` check version

`vagrant init` - initializes a new Vagrant environment by creating a Vagrantfile

`vagrant init precise64 http://files.vagrantup.com/precise64.box` - init vith VM

`vagrant up` - run VM

`vagrant ssh` - go into VM

`vagrant destroy` - kill VM

`vagrant reload` - restart VM

`vagrant status` - show status


[Content](#content)


### Vagrant help

`vagrant --help` show info 
`vagrant box -h` show info  about `box` command
`vagrant box  prune -h` show info  about `box prune` command


[Content](#content)


### Lookup PATH

When you run any vagrant command, Vagrant climbs your directory tree starting first in the current directory you are in.

```txt
/home/user/projects/la/Vagrannile
/home/user/projects/Vagrannile
/home/user/Vagrannile
/home/Vagrannile
/Vagrannile
```


### Versions

```Vagrantfile
VAGRANTFILE_API_VERSION ="2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
config.vm.box ="precise64"
end
```

As of date the current config version of vagrant is "2"
There are currently only 2 versions supported.
"1" and "2"
Version "1" config files for Vagrant version 1.0.X
Version "2" for 1.1+ leading up to 2.0.x

You can specify Vagrant version requirements in the Vagrannile to use a specific version.

```Vagrantfile
Vagrant.require_version ">=1.3.5"
```

the Vagrannile will only load if the version loadingit is Vagrant 1.3.5 or greater.



[Content](#content)




### Boxes 

Vagrant boxes are just 'Templates'

Boxes contain our base operating systems already setup

Manage Boxes such as:

`vagrant box list` - show list images

`vagrant box add <name>` - add image

`vagrant box remove <name>` - remove image

`vagrant box remove precise64` - remove image precise64


### Version Control

Vagrant creates a directory called `.vagrant/`

It keeps track of guest machine:
• IDs
• locks
• Configurations and more

this folder `should be ignored by GIT`


[Content](#content)


### Sharing folders

Folders can be shared between the hosts and the guest virtual machine:

• Use our own editors of choice
• By default this is done by sharing your Project directory to a folder on the guest machine in /vagrant
• We can actually change this behavior to where and what we want

```Vagrantfile
config.vm.synced_folder "src/", "/server/website"
end
```


```Vagrantfile
Vagrant.configure("2") do |config|
# other config here
config.vm.synced_folder "src/", "/srv/website"
End
```

There are several parameters that we can pass in the configs in order to configure synced folders.
We are going to go over some of the basic ones now.

#### Disaabling

```Vagrantfile
Vagrant.configure("2") do |config|
config.vm.synced_folder "src/", "/server/website", disabled:true
end
```

#### Modifying the Owner/Group

```Vagrantfile
Vagrant.configure("2") do |config|
config.vm.synced_folder "src/", "/server/website", 
owner: "root", group: "root"
end
```

[Content](#content)




### Network

Vagrant automatically sets up networking so that developers can start working right away on the virtual machine.
• Forwarding Ports:
A forwarded port exposes a port on the guest machine as a port on a host machine.
Today let’s do port 80 for a web server as an example.
We would modify our Vagrantfile to look like the following:

```Vagrantfile
config.vm.network "forwarded_port", guest:80, host:8080
```

After the Vagrannile is edited we would run a `vagrant reload`

to test run in VM `sudo python -m SimpleHTTPServer` and navigate to http://localhost:8080



[Content](#content)



### Teardown

`vagrant suspend` -  Suspending allows you to quickly clean up and quickly resume

Acer we suspend our virtual machine `no more CPU or RAM resources are consumed` on the host machine for the Vagrant environment, `but disk space continues`
to be used.

`vagrant halt` - Will cleanly shut down your virtual machine (probably reboot)

`vagrant halt --force` - If you need to shut down the server without a clean shut down we can force it.

`vagrant destroy` -  This will completely remove our machine from the environment and we need create `Vagrantfile` and all operations


[Content](#content)


### Provisioning and Provisioners

Base Vagrant boxes are bare.
Only the minimal function such as SSH is installed after a base box install.

We have the option to install software on our base box but that would have to be done via shell scripts, configuration management systems, or good ole manual command-­‐line entry installations.

Vagrant allows us to do automatic provisioning.
This is done when we run a 'vagrant up'.

`Provisioners` can be used such as: Shell scripts, Chef, Puppet.
There are also Several plug-­‐ins available too.

#### Automatic Provisioning

 - Shell Scripts
 - Chef
 - Puppet
 - vagrant plugins



[Content](#content)


 
### Private Networking

Private addresses that are not accessible from the public internet DHCP

We can use DHCP to assign these IP addresses within these private networks automatically

```Vagrantfile
Vagrant.configure("2") do |config|
config.vm.network "private_network", type: "dhcp"
end
```

#### STATIC IP

We can use DHCP to assign these IP addresses within these private networks automatically

```Vagrantfile
Vagrant.configure("2") do |config|
config.vm.network "private_network", ip: "192.168.70.4"
end
```


#### Disable Auto-­‐Configuration

```Vagrantfile
Vagrant.configure("2") do |config|
config.vm.network "private_network", ip: "192.168.70.4",
auto_config: false
end
```


[Content](#content)


### Public Networking

#### DHCP

We can use DHCP to assign these IP addresses within these public networks automatically

```Vagrantfile
Vagrant.configure("2") do |config|
config.vm.network "public_network"
end
```


#### STATIC IP


```Vagrantfile
Vagrant.configure("2") do |config|
config.vm.network "public_network", ip: "192.168.0.17"
end
```


#### Default Interface

```Vagrantfile
Vagrant.configure("2") do |config|
config.vm.network "public_network", bridge: 'en1: Wi-Fi (AirPort)'
end
```
[Content](#content)



### Running multiple machines

```Vagrantfile

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
 config.vm.box = "precise64"
 config.vm.box_url ="hyp://files.vagrantup.com/precise64.box"


# Setup for Web Server
 config.vm.define "web" do |web|
  web.vm.hostname ="web"
  web.vm.box ="apache"
  web.vm.network "private_network", type: "dhcp"
  web.vm.network "forwarded_port", guest: 80, host: 8080
  web.vm.provision "puppet" do |puppet|
  puppet.manifests_path = "manifests"
  puppet.manifest_file ="default.pp"
 end
end

# Setup for MYSQL DB SERVER
 config.vm.define "db" do |db|
  db.vm.hostname ="db"
  db.vm.box ="mysql"
  db.vm.network "private_network", type: "dhcp"
 end
end
```

[Content](#content)

### Boxes

Custom Boxes:

- Create your linux distro of choice
- Pre Bake your applications on these boxes

Why does Vagrant use boxes?
- Saves time
- Gives us disposable infrastructures
- We get portability with our boxes

#### Box Format

```txt
$ tree
.
├── Vagrannile
├── box-­‐disk1.vmdk
├── box.ovf
└── metadata.json
0 directories, 4 files
```


#### Basic Box Management

• Managed globally per user not per project 
• Boxes are mapped in Vagrant to a logical name in which you name

This name mapping maps to our `config.vm.box` settings in our Vagrantfile to the actual Box we are building our machine from.
• Boxes are just Files

`vagrant box`
Usage: vagrant box <comand> [<args>]

Available subcommands:
add
list
remove
repackage


`vagrant box add`

`vagrant box add precise64 hyp://files.vagrantup.com/precise64.box`
Downloading....

`vagrant box list`
Precise64 (virtualbox)

`vagrant box remove precise64 virtualbox`

`vagrant box repackage` - Will re package our box in to the original `pacakge.box` file

#### Building Boxes From Scratch

• Create a brand new Virtual Machine inside VirtualBox
• Use dynamically sized drives and set appropriate levels on RAM
• remove un necessary components
• Install from scratch the Operating System of choice

Configure the Operating System:
- a Vagrant user must exist
- SSH server must be installed and configured to run on system boot
- For Vagrant defaults be sure to use the public vagrant key

We can customize this however different in the `config.ssh.private_key_path` to point to our own private key if we wish


```sh
mkdir /home/vagrant/.ssh
chmod 700 /home/vagrant/.ssh
cd /home/vagrant/.ssh
wget --no-check-certificate 'https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub'-O authorized_keys
chmod 600 /home/vagrant/.ssh/authorized_keys
chown -R vagrant/home/vagrant/.ssh

```
In ssh config be sure to also disable requiretty


At this point we would now need to install Guest additions.
Linux based OS will need kernel headers and build tools 
For example on Ubuntu and mounting the iso you installed ubuntu with:

```sh
sudo mount /dev/cdrom/media
...
sudo sh /media/cdrom/VBoxLinuxAdditions.run
```

Additional Software for our Box

Install the software and configure it how you like
You can even pre setup and have the box ready for Chef and Puppet
Avoid the well known linux virtual machine network device issue with:

```sh
rm /etc/udev/rules.d/70-persistent-net.rules
mkdir /etc/udev/rules.d/70-persistent-net.rules
rm -rf /dev/.udev/
rm /lib/udev/rules.d/75-persistent-net-generator.rules
```

Be sure to Minimize your Final Box size!
- Uninstall unnecessary packages
- Remove build tools and linux-headers

Zero out the virtual hard drive with zeroes ad delete the zero-filled file.
Example on Ubuntu:

```sh
dd if=/dev/zero of=/EMPTY bs=1M
...
rm -f /EMPTY
```

Packaging it all Up


```sh
vagrant package --base la_new_box
```

Setting Vagrantfile Defaults

```Vagrantfile
Vagrant::Config.run do |config|
 config.ssh.username="vsvdev"
end
```

`vagrant package --base my_new_box --vagrannile Vagrantfile`

[Content](#content)
