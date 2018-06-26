# Deployer's Guide

> Calamares is designer to be thoroughly configurable and suitable for
> a wide range of use cases, but it is not an end-user application,
> nor is its configuration something end-users should be concerned
> about. Deployers of Calamares -- that is, distro's and OEMs who use
> Calamares for system installation -- should be configuring
> the application.

* [Working with modules](https://github.com/calamares/calamares/blob/master/src/modules/README.md)
* [Setting up branding](https://github.com/calamares/calamares/blob/master/src/branding/README.md)
* [Known Issues](Deploy-Issues)

_Distro-specific deployment information may be added in pages linked from_
_here, and it must clearly be declared as such._


## Configuring Calamares

Calamares is designed to be thoroughly configurable and suitable for a wide
range of use cases.

End users are not supposed to deal with configuration issues, so all
configuration is done through YAML configuration files. The files
shipped with Calamares are **sample** configuration files,
which also contain the documentation for the settings in each file.

See [the configuration guide](Deploy-Configuration) for more details.

Distributions are **strongly urged** to package configuration files
separately from Calamares itself, and to install them in `/etc/calamares`.
It is **not recommended** to edit or patch the configuration files
included with Calamares for production use -- they are examples.

Starting with Calamares 3.2.2, the sample configuration files are no
longer installed by default (the option `INSTALL_CONFIG` is now switched
off).

## LUKS deployment

See article: [LUKS deployment advice](Deploy-LUKS).

## OEM Modes

Calamares supports so-called "OEM mode", which can be used when an OEM
installs an image to a piece of hardware (e.g. a laptop) where the
installed image boots to a desktop where the user (e.g. the person
who has purchased the laptop) can complete installation by customizing
the installation (e.g. by picking another username).

An OEM can use Calamares in two different situations: for creating
an image before the device is delivered to the customer, and
for configuring the device (by the customer) after it is delivered.
These are documented in the next two sections.

All OEM modes are enabled by setting the *dontChroot* key in
`settings.conf` to the value *true*.

### OEM Imaging (pre-delivery)

> This is about setting up a sensible configuration for Calamares
> so that it writes an OS image with an autologin user.

(TODO)

### OEM Configuration (post-delivery)

> This is about the configuration of Calamares that should be
> written into the image, so that on first boot it can act as
> a "first run installer".

In post-delivery configuration, an complete installation is already present on 
the storage medium (e.g. SD card) in a device. The device boots to a 
graphical desktop environment. It is recommended to have Calamares
autostart in that environment, or to make Calamares prominently
visible in the graphical environment (e.g. an icon on the desktop).

The following Calamares modules (from the default list) are suitable
for use in a first-run installer situation:

 - *welcome*, to allow installer-language selection.
 - *users*, to create a non-default user in the system.
 - *locale*, *keyboard* for localization and individual configuration.
 - *plasmalnf*, for KDE-based distributions with Plasma Desktop.
 - *finished*.

The following are useful in the *exec* section:
 - *machineid*, for DBus.
 - *users*, *locale*, *keyboard*, *localecfg* to modify the configuration
   according to the selections made previously.
 - *networkcfg*, *services*, *displaymanager*  for additional initialization
   that cannot be done before the machine is customized.
 - *packages*, to update packages, install localizations, remove packages
   (e.g. Calamares itself, since it is no longer needed after the first run).
 - *process*, *shellprocess* and *contextualprocess* for running arbitrary
   shell commands. This can be used to, e.g. resize the filesystem image
   to fill the entire card, or to configure specifics of the image that
   are only known at runtime.

## Known Issues

> These are (historically) documented known "gotchas" when deploying
> Calamares. Typically they are configuration issues and bugs in
> external tools used by Calamares during system installation.

Known issues have moved to a [separate file](Deployers-Issues.md).
