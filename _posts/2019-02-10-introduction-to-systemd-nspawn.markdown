---
layout: post
title:  "Introduction to systemd-nspawn"
date:   2019-02-08 10:32:23 -0600
categories: sysytemd-nspawn
---
## Containers for the desktop

Managing software on your Linux desktop can be messy; installations via `Pacman/Apt/DNF` are typically global, dependencies can conflict, and running multiple versions of the same software can be challenging.

Additionally, as you install more and more software, they begin to pollute your home directory.

## Why Systemd-Nspawn

There are a lot of container options out there. Most people in the software world have heard of `Docker`. However, Docker is what is known as an application container.

Application containers are designed to run only one service and they typically communicate with each other through exposed network ports. While totally possible, creating separate Docker containers for every single application you want to install would be overkill and an exercise in patience.

Therefore, a more practical solution would be to use a system container. These are a happy medium between a VM and an application container.

When it comes to system containers the most popular option seems to be `LXC`. While it is a fine choice for production servers due its tooling, features, and resilience, it is a bit heavy for my use case of isolating packages installed on my desktop.

A far simpler solution would be systemd-nspawn. A tool that comes already installed on most modern Linux distributions since it is part of systemd. This also means systemd-nspawn can leverage systemd as its daemon rather than bringing its own, like [LXD](https://linuxcontainers.org/lxd/) and [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/).

## Installing Dependencies   
For this tutorial I am using Arch Linux and will be creating Arch Linux based containers, so I will need to install `arch-install-scripts`. This does not mean I am limited to just Arch Linux containers: creating Ubuntu/Debian containers are just as simple, but leverage a different set of tools. Refer to the [Arch wiki](https://wiki.archlinux.org/index.php/Systemd-nspawn#Create_a_Debian_or_Ubuntu_environment) if you wish to create an Ubuntu/Debian container.

## Creating Your Container
I'll be doing the majority of the setup as root:
```
sudo su
```

To create your first systemd-nspawn container you must first create a directory in which the container will reside. While this directory can be located anywhere, the convention is to place this new directory in `/var/lib/machines/`. Machinectl will look at this directory by default.

For my purposes I will call this container beancount, as it will hold all of the software I use related to the accounting software [Beancount](http://furius.ca/beancount/), such as Dropbox, Fava, and Visual Studio Code (for beancount/spellcheck extensions).  

```
mkdir /var/lib/machines/beancount
```
Next you install Arch Linux on the container. We don't want to install linux or linux-firmware since we will be leveraging the host machine for those packages. To exclude them we use the `--ignore` flag. The `-i` flag will enable manual confirmation of packages, otherwise pacstrap will blindly install the whole base Arch Linux environment. The `-c` flag will tell pacstrap to use your host machine's package cache rather than the target machine's cache. 
```
pacstrap -i -c /var/lib/machines/beancount base --ignore linux --ignore linux-firmware
```

Once the installation is complete you need to shell into the container:
```
systemd-nspawn -D /var/lib/machines/beancount
```
It is important that we set a password for our root user:
```
passwd
```

And at this point it is a good idea to create a user. Most applications will refuse to run as root. 

These two commands will add a user named patrick to the wheel group, and assign it a password.

```
useradd -m -G wheel -s /bin/bash patrick
passwd patrick
```

You'll also want to install sudo for this new user. 
```
pacman -Sy sudo
```
Then add the wheel group to the list of sudoers by navigating to `/etc/sudoers` and deleting the comment for the line `%wheel ALL=(ALL) ALL`

If you find that your terminal is not functioning properly and you're using urxvt, you need to install the terminfo for urxvt:
```
pacman -S rxvt-unicode-terminfo
```


We will also start using machinectl to start and shell into our containers. However, machinectl can not shell in as root user by default. To fix this, edit your `/etc/securetty` file inside of your container and add `pts/0`.

To exit the container, simply tap Ctrl + ] three times.

## Container Management
You should be able to manage your container with machinectl now.

The command below should list the container you just created, and if you don't see it something went wrong:

```
machinectl list-images
```

At this point your container is not running, so you can start your container with:
```
machinectl start beancount
```

You should then see your container in the list of running containers:
```
machinectl list
```
To invoke a shell to your container simply run:
```
machinectl shell beancount
```
And just like before you can terminate the connection to the container by hitting Ctrl+ ] three times.

If you're no longer going to use your container you can shut it down with this command:
```
machinectl poweroff beancount
```

## Systemd Integration
While you may not want every container to start on boot, you'll most likely want some containers to run all of the time.

In this event you can register your container as a service with systemd:

```
systemctl enable systemd-nspawn@beancount.service
```

Disabling is just as simple:

```
systemctl disable systemd-nspawn@beancount.service
```

Since this container is just for my accounting software stack, I will opt out of registering this container as a systemd service.

## Conclusion

Hopefully you now have a basic understanding of how and when to use systemd-nspawn. In the next tutorial I will discuss how to enable graphical applications and sound in your container. 

For questions and comments feel free to email me at [pskiba@posteo.net](mailto:pskiba@posteo.net).