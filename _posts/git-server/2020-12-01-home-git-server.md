---
title: Setup a Git Server in Your Home
date: 2020-12-10 11:58:47 +00:00
modified: 2020-02-02 16:49:47 +00:00
tags: [tech]
description: Setup a git server in your home and access on other computers in your home network
image: "/assets/img/ogp.png"
---

I installed [Ubuntu server 20.04](https://releases.ubuntu.com/20.04/) (on wired network to speed up) on my solid Mac Book Pro 2009 Mid model (128 GB HDD, 8 GB RAM). It is still such a great machine. Except Xcode, every programming environment I was in need of was running very fine on it. 

After finishing the server installation I installed [Xfce](https://www.xfce.org) DE as it is lightweight. I felt it is too primitive. So, I uninstalled it and installed [Gnome](https://www.gnome.org) basic DE. 

You can install *minimal Gnome* or _minimal Gnome Desktop_

#### Installing minimal Gnome on Ubuntu 20.04

<pre><code>$ sudo apt install gnome-session gnome-terminal
$ reboot
</code></pre>

#### Installing minimal Gnome Desktop on Ubuntu 20.04
<pre><code>$ sudo apt install tasksel
$ sudo tasksel install ubuntu-desktop-minimal
$ reboot
</code></pre>

Once the system reboots, you should be showing a login screen. Now, installing a DE for Linux is complete.

## Install GitLab server

You can install Gitlab server in two ways - by running a script or by downloading Gitlab package.

## Method 1:

Update, just in case! :)
<pre><code>$ sudo apt update</code></pre>

<br>
Install Gitlab package dependencies
<pre><code>$ sudo apt-get install -y curl openssh-server ca-certificates</code></pre>

<br>
Download Gitlab instance
<pre><code>$ curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash</code></pre>

<br>
Now install gitlab-ce package
<pre><code>$ sudo apt-get install gitlab-ce</code></pre>


## Method 2:
<br>
Change your directory to Downloads to download the package
<pre><code>$ cd ~/Downloads</code></pre>

<br>
Get the Gitlab package
<pre><code>$ wget --content-disposition https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/bionic/gitlab-ce_13.2.1-ce.0_amd64.deb/download.deb</code></pre>

<br>
Get the Gitlab package
<pre><code>$ sudo dpkg -i gitlab-ce_13.2.1-ce.0_amd64.deb</code></pre>

<br>
Now is the time to configure Gitlab. `/etc/gitlab/gitlab.rb` is the file where you mention configurations you wanted. Mostly you would not need to modify this file so much. 
<pre><code>$ $ sudo vi /etc/gitlab/gitlab.rb</code></pre>

<br>
Search for the term `external_url`.  Give `http://localhost:80` for `external_url` value. Save the file, exit Vi editor and `reconfigure` gitlab service by running the following command.

This command will take some time to finish. 
<pre><code>$ sudo gitlab-ctl reconfigure</code></pre>

<br>
Once reconfiguration is done, start the service.
<pre><code>$ sudo gitlab-ctl start</code></pre>

<br>
Now, you can check the status of the Gitlab server
<pre><code>$ sudo gitlab-ctl status</code></pre>

<br>
Now, open [http://localhost:80](http://localhost:80) in your browser. You must see something like this. Your username and password would be `root` and your root password. 
Also, you can customize Gitlab UI by changing 
* Banner image to your startup name
* favicon
* Navigation bar color
* Syntax highlighting theme for source files etc.

<figure>
<img src="/assets/images/gitlab.png" alt="">
</figure>

## Post Installation: Stop Linux from sleeping or getting suspended

As I installed GitLab server on my MBP, I did not want the screen lid to be open all the time. So, you can edit `/etc/systemd/logind.conf` and edit system behavior such as `HandleSuspendKey`, `HandleLidSwitch` etc. 

I changed `HandleLidSwitch` to `ignore` so that Linux does not suspend even after I close thie lid. 

And, suppressed `Automatic Suspend` to off for both power and battery run options.