+++
title = "Installing Dropbox on Ubuntu 13.10 "
date = "2014-03-19"
+++

Just a quick post on something that has been annoying me for a
few days. I recently installed Ubuntu 13.10 and couldn't get the Dropbox sync program to
work. After much hunting for information and a bit of trial and error I have found a
solution. My problem was that after the install the Dropbox program would say that my
computer was not linked to an account, but gave no option to add an account. In past
installs the Dropbox program would throw up a dialog after installation to enter your
account details, but on my 13.10 install it did not. My solution was to uninstall Dropbox
and then reinstall using the instructions
[here](http://www.dropboxwiki.com/tips-and-tricks/install-dropbox-in-an-entirely-text-based-linux-environment#Step-by-step_version)

Once I ran the script at step 7, the add account dialogue box popped up and now everything
is fine.

It also seems that the Dropbox notification indicator is broken (does not show) in 13.10.
To fix this install package `libappindicator1` then log out, and then log back in (or
issue `$ dropbox stop && dropbox start` on the command line).
