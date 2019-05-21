# Deployer's Guide

> Calamares is designer to be thoroughly configurable and suitable for
> a wide range of use cases, but it is not an end-user application,
> nor is its configuration something end-users should be concerned
> about. Deployers of Calamares -- that is, distro's and OEMs who use
> Calamares for system installation -- should be configuring
> the application.

The primary documentation for Calamares configuration is in the
source code and the configuration files themselves. The source
code contains two overview files:

* [Working with modules](https://github.com/calamares/calamares/blob/master/src/modules/README.md)
* [Setting up branding](https://github.com/calamares/calamares/blob/master/src/branding/README.md)

This guide contains high-level documentation on configuration and
deployment but for details you will have to consult the configuration
files for individual parts of Calamares.

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

[OEM Configuration Guide](Deploy-OEM).

## Styling Calamares

> Calamares follows the desktop styling if the Qt Platform does so.
> There is additional styling which can be applied through the
> Branding module and stylesheets.

The [Branding configuration][branding.desc] sets a few style parameters --
window behavior and colors and icons. Using only that file, Calamares
will largely follow the desktop styling.

It is possible to style Calamares at a much more fine-grained level
using Qt Stylesheets. These are basically CSS styles where the CSS
selectors apply to widget classes or specific widgets.

An overview of Qt Stylesheets is [here][https://doc.qt.io/qt-5/stylesheet-examples.html].

If a branding component ships a `stylesheet.qss` next to the `branding.desc`
file, that stylesheet will be loaded and used by Calamares.

Class selectors can be applied in Calamares, and the following
stylesheet makes all the combo-boxes in the application green:
```
QComboBox { background: green; }
QComboBox QAbstractItemView { background: lightgreen; }
```

Individual widgets can also be styled by using their name as CSS id.
The [example `stylesheet.qss`][stylesheet.qss] lists many top-level
widgets that can be styled, such as:
 - *mainApp* The top-level window
 - *sidebarApp* The application's sidebar (progress tree, on the left)
 - *logoApp* The application logo (top-left)
Some modules also have named widgets, such as the *partition* and *license*
modules.

For **debugging** purposes and for trying out styles, the *debug window*
can be used. The *tools* tag contains (as of Calamares 3.2.9) these buttons:
 - *reload stylesheet* which does what it says: reloads the stylesheet file
   and applies it to the application. This can be used to test changes in
   the stylesheet without re-starting Calamares.
 - *widget tree* which dumps the names of all the widgets in the application
   to the log file (usually `session.log`, or to standard output). This can
   help to find out the name of a particular widget which you wish to style.

[stylesheet.qss]: https://github.com/calamares/calamares/blob/master/src/branding/default/stylesheet.qss
[branding.desc]: https://github.com/calamares/calamares/blob/master/src/branding/default/branding.desc

## LUKS deployment

See article: [LUKS deployment advice](Deploy-LUKS).

## Known Issues

> These are (historically) documented known "gotchas" when deploying
> Calamares. Typically they are configuration issues and bugs in
> external tools used by Calamares during system installation.

Known issues have moved to a [separate file](Deployers-Issues.md).
