+++
title = "Dual Booting Ubuntu and Windows on Surface Pro 3"
date = "2015-02-28"
+++

As part of my PhD I was generously given a brand new Microsoft Surface Pro 3.
Unfortunately, as I have been an Ubuntu user for the better part of a decade, using
Windows again was a less than optimal experience. Having read [many blog
posts](http://www.reddit.com/r/surfacelinux) about the Surface 3 hardware not being
supported by the current Ubuntu kernel I resolved to wait and battle on with Windows 8.1.
However, after a sever bout of [yak shaving](http://en.wiktionary.org/wiki/yak_shaving) I
managed to get the latest 15.04 beta 2 working (mostly).

## Back the hell up!

Before you attempt this with your own shiney Surface Pro 3 (SP3) I highly, strongly,
emphatically recommend that you grab a 8Gb USB thumb drive and make a Windows recovery
disk (via Search > "Create a recovery drive"). The SP3 does have its own recovery
partition, however to get the dual boot working you are going to be messing with partition
tables and it is a really good idea to have a stand alone recovery option in case
everything goes to hell. Also, as always, back up your personal files. They should be safe
with the repartitioning, but you never know. You have been warned!

## Get bootin'

I set up the dual boot by following [this very informative, and well illustrated, blog
post from David
Elner](http://blog.davidelner.com/dual-booting-ubuntu-14-10-on-the-surface-pro-3/).
However, there were a few key differences.

1. I used the latest [15.04 Beta
   2](http://cdimage.ubuntu.com/ubuntu-gnome/releases/vivid/beta-2/) build of Ubuntu Gnome
   instead of the the vanilla 14.10 Ubuntu David used. 15.04 is based on on the 3.18
   kernel and was hoping this would provide some performance improvements. I prefer Gnome
   as a desktop environment and it also has multi-touch features for tablets. This is just
   a preference and when [Ubuntu Touch](http://www.ubuntu.com/tablet) is released for
   general hardware that may take over as the default.
2. I did not install the wifi and keyboard drivers as the ones David used were designed
   for the 3.17 kernel. However, I found that with 15.04 the wifi and keyboard are working
   fine. The touch pad doesn't work but as it is possibly the worst touch pad I have ever
   used I didn't see that as much of an issue.
3. I did not install rEFInd or mess about with any of the EFI signing. It is not required
   to make the dual boot work and I am fine with the red screen of doom that awaits any
   sole who dares disable secure boot!
4. I changed the default boot OS in grub so that if I boot the SP3 without a keyboard
   attached it will boot into Windows. This is because, at the moment, Gnomes tablet
   support is a bit basic (but it will be getting better).   

### The end result

So after all of the above I have working Ubuntu install on my SP3:

- The keyboard works, though not the touchpad (but who cares it was terrible anyway and I
  always use an external mouse).
- The wifi works, but can be a bit temperamental.
- The touch screen works fine with your fingers and registers the Pen. However, as there
  are no drivers for the Surface Pen the right click function and other buttons do not
  work.
- The external display connections work fine. However, the small dimension / stupidly high
  resolution combination of the screen can lead to some weird text scaling effects, your
  mileage may vary depending on your chosen desktop environment (I spent alot of time
  messing around with the Gnome Tweak Tool).

So that's it. Other users have reported fun and games with Windows overwriting grub and
have resorted to putting their boot partition on an MicroSD card. I haven't had this
problem yet but it is early days. In the near future I will attempt to run the latest
mainline kernel builds (3.19 and 4.0) to see if they get the touchpad working, but at the
moment I am very happy with the outcome and my SP3 is now a viable work computer (even if
it is still a bit of a toasterfridge)
