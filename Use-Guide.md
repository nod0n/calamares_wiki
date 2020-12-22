# User's Guide

> This section of the wiki is about **using** Calamares to install
> your system. If you are reading this, it is probably because
> your distribution doesn't have more detailed specific instructions.

Calamares is highly modular and configurable. This
guide explains what Calamares is **capable** of.
Your distribution might not use all of it, or might have
switched some parts off.

## Common Modules

> For nearly all installations, and all configurations of Calamares,
> you will see these modules used.

![Calamares Sidebar](img/sidebar.png)

### Welcome

Welcome to the installer! Here you will find a nice big friendly
logo for your Linux distribution.
Calamares does some basic checking here to see if the system is usable
for installation, and will warn you if something seems to be wrong.

![Calamares Warnings](img/welcome-warning.png)

You can change the language of the installer here.
Over fifty languages are supported.
There may be some buttons that link
to your distribution's website (for instance, so you can read
the release notes).

![Calamares Language Selection](img/welcome-buttons.png)

Calamares checks if there is internet connectivity (this is used
by many distributions for package updates) by loading a well-known
page. This is configured by the distribution.


### Location

Where are you? Tell Calamares your timezone.
This also informs some choices like keyboard layout, time and date formats, and currency formats.

Optionally, Calamares can be configured to use GeoIP services to
guess your location. This will use a GeoIP provider configured by the
distribution.


### Keyboard

What does your keyboard look like? Distinguish QWERTY from QWERTZ and all the other
interesting layouts.


### Partitions

What part of the disk do you want to overwrite?

#### Automatic partitioning

If you choose an automatic partitioning option, you will have the option of
encrypting your new installation. You will have to enter your a passphrase
two times. An encrypted system with a week passphrase, isn't very secure and
therefore better choose a good passphrase.

##### Replace a partition

This options is not always available. In general you can replace an existing
installation, but only the existing partition will be replaced. You can keep
any other operating system that is not selected for replacement on the same
disk.

##### Erase disk

Erase the complete disk and replace it with your new installation. In this
mode all prior installed operating systems on the selected disk will be
deleted and you new operating system will be installed.

#### Manual partitioning

It is up to you how you partition your new installation. You will need some
knowledge about the boot process to successfully install the system.

You will have to choose on which device you would like to install your
operating system. If you are on a somewhat modern computer it is likely that
it has EFI. With EFI you will need to use an EFI partition. If you are using
GPT on BIOS you will need a "grub bios" partition.

##### Encrypted partitions

You can create partitions and select the size, file system, mountpoint and
flags. If you enable the "Encrypt" checkbox, you have to enter a passphrase
twice and the partition will be encrypted with luks. You filesystem will be
stored in the luks container. This is specially useful, if you would like to
encrypt your system, but you don't like the default filesystem (ext4).

### Users

Your login information, who are you and what is your password?


### Summary

Before making real changes to the system, Calamares summarizes what it is going to do.
This is one more chance to back out before anything irrevocable happens.


### Install

When you reach this step, Calamares does the **actual** work of doing
the installation. Since this can take a long time, you can watch a
slideshow here. Some distributions even let you play a game while you wait!


### Finish

All done. Calamares will provide a finaly summary and allow you to re-start your machine into your
newly installed Linux operating system.



## Optional Modules

> These are modules for unusual cases or special distributions,
> and you will probably not see them.


### Installation Tracking

> This module is disabled by default, and its default configuration
> turns off all tracking as well. No distribution is known to use it.

Calamares can be configured to do installation tracking. If it is,
you will get a page in the installation process that asks you to
enable installation tracking. If, and only if, you enable installation
tracking, then Calamares will send information **once only** about
your hardware to the server configured by the distribution.

![Calamares Tracking Page](img/tracking.png)

This is used by distributions to count how often they are installed,
and on what kinds of machines -- which helps the distribution tailor
its packages to the machines its users typically use.

When Calamares sends information about the hardware, it sends:
- the make and model of your CPU
- the amount of main memory
- the total amount of attached disk

As part of sending this information, Calamares necessarily makes
your IP address known to the receiving party.


