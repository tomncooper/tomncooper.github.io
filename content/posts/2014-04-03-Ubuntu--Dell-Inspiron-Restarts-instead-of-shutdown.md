+++
title = "Fixing the shutdown = restart problem in Ubuntu 13.10 on Dell Inspiron 7537"
date = "2014-04-03"
+++

So another day and yet another adventure on my return journey to Ubuntu. After all the fun
I had doing my first
[UEFI](http://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) linux install,
which is a story I will save for another time, my [Dell Inspiron
7537](http://www.dell.com/uk/p/inspiron-15-7537/pd) had been working just fine. Then, for
no discernible reason, it decided that it was not going to listen to me anymore. Like a
petulant child it would not go to bed (turn off) and wanted to stay up and continue to
watch late night TV (restart).......anyway enough with this convoluted metaphor, it was
very annoying.

After much digging I found the post from DuckHook on [this
thread](http://ubuntuforums.org/showthread.php?t=2188726) was the most helpful. The first
suggestion of installing `laptop-mode-tools` and then running `laptop_mode`
made no detectable difference. However step 3 seemed to fix the problem. This involved
telling the
[ACPI](http://en.wikipedia.org/wiki/Advanced_Configuration_and_Power_Interface) not to
route [interrupt requests](http://en.wikipedia.org/wiki/Interrupt_request) which the
kernel might not have released. If these IRQ's were persisting, it could cause the BIOS
(or in this case the UEFI) to cycle when the system goes to power down.

So I edited the `/etc/default/grub` file so that the `GRUB_CMDLINE_LINUX_DEFAULT`  line
included a `"acpi=noirq"` statement and then ran `sudo update-grub`.

This fixed the shutdown problem however I did suspect that the IRQ issue may not be the
whole story. So I removed the acpi statement from the grub config file and updated grub
again. Low and behold the shutdown issue was still fixed. I would love to pretend I know
what the heck had fixed it, I suspect that updating grub reset a corrupted config file
somewhere, but I am just happy my laptop goes to bed when I tell it too!
