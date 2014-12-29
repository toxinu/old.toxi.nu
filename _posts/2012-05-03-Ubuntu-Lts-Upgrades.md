---
layout: post
title: Ubuntu Server LTS upgrades are stress test
---

<br />
<hr>
### Update
* Sept, 24 2012

This article is now deprecated, Ubuntu now have a clean upgrade script.  
Just use ``do-release-upgrade``.

<hr>
</br >

This week I had to upgrade some Ubuntu Server 10.04 to the new Canonical present.  
And as much to upgrade my Debian scares me, then Ubuntu is a fabulous stress test...

I admit to not being an Ubuntu expert ( Debian too ) but the `do-release-upgrade` is like :

> "Ohhh yeah take this Ultra Decap'Net my son !"
> " .... But ..but what's dazzz sh... ?"
> "I don't know, shut up, press it and close your eyes"

But this is the recommended solution.

{% highlight bash %}
apt-get update
aptitude full-upgrade
apt-get install update-manager-core
grub-install /dev/sda
update-grub
reboot
{% endhighlight %}

Your server is ready to upgrade.

{% highlight bash %}
do-release-upgrade -d
{% endhighlight %}

The **-d** option say that you want to do develpment upgrade, cause this isn't officially supported.

__/!\Warning/!\\__

You have to re-upgrade grub after the upgrade.  
So Say **NO** when upgrade script ask you to reboot.  
Press x to destroy session and return into your initial terminal.

{% highlight bash %}
grub-install /dev/sda
update-grub
reboot
{% endhighlight %}

Where am I wrong with `grub-install` and `update-grub` ?  
Ok ok, I haven't search more but packages must do it ? With os-prober, etc...

But, the job is done.

<hr>

If for X reason your __felt__ into a Grub Rescue hole this can help you:

{% highlight bash %}
set prefix=(hd0,2)/boot/grub
set root=(hd0,2)
insmod (hd0,2)/boot/grub/linux.mod
linux /vmlinuz root=/dev/sda2 ro
initrd /initrd.img
boot
{% endhighlight %}
