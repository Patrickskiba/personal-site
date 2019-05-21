---
layout: post
title:  "Graphical applications in systemd-nspawn"
date:   2019-03-21 05:30:01 -0600
categories: sysytemd-nspawn
---
This is a continuation of my previous post on systemd-nspawn. If you are not familiar with the basics of systemd-nspawn, I suggest going back and reading my previous post [here](https://patrickskiba.com/sysytemd-nspawn/2019/02/08/introduction-to-systemd-nspawn.html). 

Setting up GUI applications using nspawn is actually quite easy; in most cases we can simply connect our container to our host's X server. 

In some cases where an application relies heavily on the user's graphics card, i.e., gaming or deep learning, we will also need to pass in the device files for our host's GPU drivers.

For this tutorial I'll be creating a container called `firefox-container` and installing Firefox in it.

As a side note, a few reasons why you might want to install an internet browser inside of a container would be:

 - You want to use a vpn to watch shows not available in your country but don't want other traffic to route through the vpn.
 - You want a clean instance of the browser without your typical browser plugins, but don't want to disable and re-enable those plugins on your primary browser. This would be helpful to web developers that want to test their website on the most common version of the browser.
 - You want to sandbox your browser from the rest of the file system for additional security.

## Create a container
To start off, we're going to make a simple container that will connect to your host's X server. This will allow your container to display apps as if they were natively being run on your host.

I'll briefly run down the commands I used to create this container, but for more information reference the following [post](https://patrickskiba.com/sysytemd-nspawn/2019/02/08/introduction-to-systemd-nspawn.html).
```
sudo su

mkdir /var/lib/machines/firefox-container

pacstrap -i -c /var/lib/machines/firefox-container base --ignore linux --ignore linux-firmware

systemd-nspawn -D /var/lib/machines/firefox-container

passwd

useradd -m -G wheel -s /bin/bash patrick

passwd patrick

pacman -Sy sudo

pacman -S rxvt-unicode-terminfo

pacman -S firefox
```
Additionally, I added the wheel group to the list of sudoers in `/etc/sudoers`

## Connecting to X server
On the **host** OS you will need to install [Xhost](https://wiki.archlinux.org/index.php/Xhost). This permits us to create a list of host names that are allowed to connect to the X server.

```
pacman -Sy xorg-xhost
```
Then run the following command to enable local connections to the X server:

```
xhost +local:
```
Check what display your **host** is using. Typically this will return :0, but if you are using multiple graphics cards it could be somthing different.

```
echo $DISPLAY
```

We can then boot up our container as our non-root user and pass in the necessary files and environment variables required for graphics to work in the container. However, there are some items we need to consider before running the commands.

The contents of `/tmp/.X11-unix` contain files necessary for X to function, and it is therefore important that our container does not overwrite these files, so we will be binding these files in read-only mode.

We will also need to set the environment variable of DISPLAY on the container to the same value as our host.

The full command is shown below:

```
systemd-nspawn -D /var/lib/machines/firefox-container \
	-u patrick \
	--bind-ro=/tmp/.X11-unix \
	--setenv=DISPLAY=:0
```

## Adding sound to our container
I am going to assume you are using pulseaudio for sound. We will need to enable pulseaudio as our default audio server and then bind your host's pulseaudio files to the container. 

Create a configuration file at `/etc/asound.conf` and paste in the following:

```
pcm.!default {
	type pulse
	fallback "sysdefault"
	hint { 
		show on 
		description "Default ALSA Output (currently PulseAudio Sound Server)" 
	} 
} 

ctl.!default {
	type pulse
	fallback "sysdefault"
}
```

We can then bind our host's pulseaudio files to the container by adding the following line to our boot command:
```
 --bind=/run/user/1000/pulse:/run/user/host/pulse
```
We also need to set the PULSE_SERVER environment variable to the location of those files we passed in:

```
 --setenv=PULSE_SERVER=unix:/run/user/host/pulse/native
```

When all is said and done we should be left with the following command to boot up a simple container worthy of running graphical applications:

```
systemd-nspawn -D /var/lib/machines/firefox-container \
	-u patrick \
	--bind-ro=/tmp/.X11-unix \
	--bind=/run/user/1000/pulse:/run/user/host/pulse \
	--setenv=DISPLAY=:0 \
	--setenv=PULSE_SERVER=unix:/run/user/host/pulse/native
```

## Installing Steam on an nspawn-container
If you plan on running applications that require direct access to your graphics card drivers, then you'll need to bind a few more files. If not, you can skip over this section.

Before we bind the driver files, the drivers must be installed on the **host**. Since I'm using an Nvidia graphics card, I'm going to check in my `/dev` directory for the occurrence of Nvidia. Those will be the files we bind our container to. If you're using an Nvidia graphics card and you don't see files such as nvidia0 or nvidia-modeset, then your drivers are not properly installed.

You can install Nvidia drivers with this command on your **host** OS:
```
pacman -S nvidia
```

If everything checks out fine then you'll want to bind these files to your container:

```
--bind=/dev/dri \ 
--bind=/dev/shm \ 
--bind=/dev/nvidia0 \
--bind=/dev/nvidiactl \
--bind=/dev/nvidia-modeset
```

The end result of your container's boot command should look like this. In this example I made a container called `steam-container`:
```
systemd-nspawn \ 
	-D /var/lib/machines/steam-container \
	-u patrick \
	--bind-ro=/tmp/.X11-unix \
	--bind=/run/user/1000/pulse:/run/user/host/pulse \
	--bind=/dev/dri \
	--bind=/dev/shm \
	--bind=/dev/nvidia0 \
	--bind=/dev/nvidiactl \
	--bind=/dev/nvidia-modeset \
	--setenv=DISPLAY=:0 \
	--setenv=PULSE_SERVER=unix:/run/user/host/pulse/native
```

Once you're in the container you'll want to install the Nvidia driver utilities for both 64 and 32 bit. Steam itself has dependencies on 32 bit graphics libraries:

```
sudo pacman -S nvidia-utils lib32-nvidia-utils
```

Finally install Steam:
```
sudo pacman -S steam
```
Reboot your container and then run `steam` in the terminal and you should have a working instance of Steam.

To have Steam automatically launch when you launch the container use the `-a` flag to provide a command to run on boot. 

In this case you'll want to add the following to the end of your container command:
```
-a steam
```
## Creating a shortcut for your launcher
Typing in these commands over and over again is impractical. Depending on your desktop environment, there are a number of different solutions. In my case, I am using dmenu as my primary launcher for applications, which uses the user's PATH environment variable to get a list of available applications.

In the case of Steam, I created a file at `/bin/steam-container` with the script to launch the container and the command to enable local connections to the X server:

```
xhost +local:

sudo systemd-nspawn \ 
	-D /var/lib/machines/steam-container \
	-u patrick \
	--bind-ro=/tmp/.X11-unix \
	--bind=/run/user/1000/pulse:/run/user/host/pulse \
	--bind=/dev/dri \
	--bind=/dev/shm \
	--bind=/dev/nvidia0 \
	--bind=/dev/nvidiactl \
	--bind=/dev/nvidia-modeset \
	--setenv=DISPLAY=:0 \
	--setenv=PULSE_SERVER=unix:/run/user/host/pulse/native
	-a steam
```

I then make the file an executable with this command:

```
sudo chmod +x /bin/steam-container
```

You should be able to type `steam-container` into your **host's** terminal and have Steam launch via the container.

## Conclusion
By now you should have a decent understanding of how to spin up systemd-nspawn containers with graphically enabled applications. 

For questions and comments feel free to email me at [pskiba@posteo.net](mailto:pskiba@posteo.net).