+++
title = "Installing 64 bit Citrix Receiver on Ubuntu 13.10"
date = "2014-04-04"
+++

Well here I am again blogging about how to fix something on Ubuntu. It is a great OS, but
as with everything in Linux, eventually you need to open up a terminal and poke it a bit.

My university has a Remote Application Service (RAS) that uses the Citrix Receiver
software to provide access to virtualised software. Great for running Matlab at home using
the uni licence. Citrix do provide a Linux version of their receiver software (packaged as
both .rpm and .deb) so big thumbs up to them. However the 64 bit .deb package is all
messed up and if you try to install it will enter you in to a never ending spiral of unmet
dependencies and broken packages!

However all is not lost. As with most things in the open source community, some bright
spark has figured out the problems and provided a fix! 
[This page](https://help.ubuntu.com/community/CitrixICAClientHowTo) on the Ubuntu
community wiki gives a detailed breakdown of what you need to do to get the receiver
working on your version of Ubuntu. I followed the first set of instruction for installing
Receiver 13.0 on 64 bit Ubuntu 13.10 and everything is working perfectly now.

Basically you have to fix a few issues with the .deb file. These issues don't involve
compiling anything so I have put my [fixed version of the .deb file]() up on this site to
save you the trouble. As far as I understand it this should work, provided you have run
the commands below (as detailed on the wiki) to install the missing dependencies:

	sudo dpkg --add-architecture i386 # only needed once
	sudo apt-get update
	sudo apt-get install nspluginwrapper lib32z1 libc6-i386 libxml2:i386 /
                             libstdc++6:i386 libxerces-c3.1:i386 /
                             libcanberra-gtk-module:i386 libcurl3:i386 /
                             libasound2-plugins:i386 libgstreamer-plugins-base0.10-0:i386
