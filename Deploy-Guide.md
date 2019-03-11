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

All OEM modes are enabled by setting the *dont-chroot* key in
`settings.conf` to the value *true*.

## Known Issues

> These are (historically) documented known "gotchas" when deploying
> Calamares. Typically they are configuration issues and bugs in
> external tools used by Calamares during system installation.

Known issues have moved to a [separate file](Deployers-Issues.md).
