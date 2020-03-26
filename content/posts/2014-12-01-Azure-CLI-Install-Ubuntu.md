+++
title = "Installing Microsoft Azure Command Line Tools on Ubuntu"
date = "2014-12-01"
+++

As part of my MRes I am using Microsoft's Azure cloud platform. I was pleasantly surprised
to see that Microsoft provide Linux support for Azure and SDK's for a lot of programming
languages. However I hit a few bumps getting the command line tools installed due to some
quirks with Ubuntu.

The easiest way to get the Azure CLI is to use the Node.js package manager `npm`:

	$ sudo apt-get install nodejs
	$ sudo apt-get install npm

This should all work fine and then you can simply install the Azure CLI using the command
below:

	$ npm install azure-cli -g

Now on other Linux distros this may work fine. However on Ubuntu 14.04 there was an issue
due to the fact that Azure CLI calls Node.js using the `node` command and on Ubuntu it is
called `nodejs`. As a result you get the error below when you call `azure`:

	/usr/bin/env: node: No such file or directory

To fix this, use the command below to tell Ubuntu that when any program calls `node` it
really means `nodejs`:

	$ sudo update-alternatives --install /usr/bin/node nodejs /usr/bin/nodejs 100

You should now be able to call `azure` and all its functions!
