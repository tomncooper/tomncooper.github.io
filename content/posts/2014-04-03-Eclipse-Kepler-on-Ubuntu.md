+++
title = "Installing Eclipse Kepler (4.3.2) on Ubuntu 13.10"
date = "2014-04-03"
+++

Recently I have been making the switch back to Ubuntu after a year of pretending I was ok
with using Windows 8. It so good to have an OS that actually puts me in control. Anyway
enough evangelising.

I [recently posted]({filename}2014-03-19-dropbox-on-ubuntu-13.10.md) about getting Dropbox
up and running on Ubuntu 13.10 which required a bit of gentle nudging to get it working.
Well another program that needs a bit of nudging is the latest build of the Eclipse IDE.

The Ubuntu repositories have an old version of (3.8.1) of Eclipse, so if you want the
latest bleeding edge build (currently 4.3.2) then you have to download the program as a
tar.gz file from the [Eclipse Foundation
website](https://www.eclipse.org/downloads/packages/eclipse-standard-432/keplersr2). 

Also before you start make sure you have the Java Development Kit (JDK) installed. You can
do this through the Ubuntu software centre by searching for "openJDK".

Once you have the eclipse tar.gz file extract it to your current folder (double click on
it and use the default archive manager). You then need to move the extracted "eclipse"
folder to the /opt folder in the root directory. To do this use the terminal to cd into
the directory holding the "eclipse" folder and run:

`$ sudo mv eclipse /opt/`

The next step is to make a symbolic link so that you can call the executable without
specifying the path each time. This link needs to go in your bin folder so first cd into
that:

`$ cd /usr/local/bin`

and then make the symbolic link to the executable:

`$ sudo ln -s /opt/eclipse/eclipse`

The next step is to create an `eclipse.desktop` file (like a program shortcut in windows),
this needs to do in the `/usr/share/applications` directory so run:

`$ sudo gedit /usr/share/applications/eclipse.desktop`
        
and copy the following into gedit:

    [Desktop Entry]
    Name=Eclipse
    Type=Application
    Exec=env UBUNTU_MENUPROXY=0 eclipse>
    Terminal=false
    Icon=/opt/eclipse/icon.xpm
    Comment=Integrated Development Environment
    NoDisplay=false
    Categories=Development;IDE
    Name[en]=eclipse.desktop

Save the file and close gedit.

The `=env UBUNTU_MENUPROXY=0` statement is very important as this fixes an issue with the
current build of Eclipse on Ubuntu 13.10 where the menus will not show at all.

After this you should just be able to launch eclipse by pressing the windows key on the
keyboard (to call up the search scope) and type "eclipse". The icon should pop up and you
can click on it to start the program. 

I have read on the forums that sometimes (depending on how you installed it and the setup
of your system) your system might use the eclipse.desktop file in
`/.local/share/applications/eclipse.desktop`. So if your eclipse is launching fine but the
menus still don't work you should try opening the .local version and add `=env
UBUNTU_MENUPROXY=0` after the `Exec` in that eclipse.desktop file.
