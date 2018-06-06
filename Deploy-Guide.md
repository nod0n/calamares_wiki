# Deployer's Guide

> Calamares is designer to be thoroughly configurable and suitable for
> a wide range of use cases, but it is not an end-user application,
> nor is its configuration something end-users should be concerned
> about. Deployers of Calamares -- that is, distro's and OEMs who use
> Calamares for system installation -- should be configuring
> the application.

* [Working with modules](https://github.com/calamares/calamares/blob/master/src/modules/README.md)
* [Setting up branding](https://github.com/calamares/calamares/blob/master/src/branding/README.md)
* [Known Issues](Deployers-Issues)

_Distro-specific deployment information may be added in pages linked from_
_here, and it must clearly be declared as such._


## Configuring Calamares

Calamares is designed to be thoroughly configurable and suitable for a wide
range of use cases.

End users are not supposed to deal with configuration issues, so all
configuration is done through YAML configuration files. The files named
here link to the sample configuration files shipped with Calamares,
which also contain the documentation for the settings in each file.

Each sample configuration file contains documentation on the options it
contains (it not, it's a bug and you should file an issue).

The top-level configuration of Calamares is done in
[`settings.conf`][settings.conf], the file which defines which modules
the system uses and whether it is in OEM (*dont-chroot*) or normal
system-installer mode. The modules listed in `settings.conf`
are loaded one by one; their configurations are documented in
the respective files (see the [source tree][modules]).

Calamares is usually shipped with *PREFIX* set to `/usr`,
so that most of its configuration is in `/usr/share/calamares`.
In the table below, `$USC` means that directory.

| File name | Deployment path (in search priority) | Purpose |
|:---------:|:------------------------------------:|:-------:|
| [`settings.conf`][settings.conf] | `/etc/calamares`, `$USC` | Main Calamares configuration file |
| [`branding.desc`][branding.desc] | `$USC/`_`brand_name`_ | Branding descriptor file, shipped with the rest of your branding component and selected in `settings.conf` |
| _`modulename`_`.conf`, e.g. `partition.conf`, `grubcfg.conf`, `unpackfs.conf`, ... | `/etc/calamares/modules`, `$USC/modules` | Configuration files for every module that needs one |
| [`displaymanager.conf`][displaymanager.conf] | | Configuration for the display manager, select SDDM, lightdm, ... |
| [`locale.conf`][locale.conf] | | Locale and TimeZone configuration. GeoIP settings. |
| [`partition.conf`][partition.conf] | | Configuration for (automatic) partitioning and swap |

[modules]: https://github.com/calamares/calamares/blob/master/src/modules
[settings.conf]: https://github.com/calamares/calamares/blob/master/settings.conf
[branding.desc]: https://github.com/calamares/calamares/blob/master/src/branding/default/branding.desc
[displaymanager.conf]: https://github.com/calamares/calamares/blob/master/src/modules/displaymanager/displaymanager.conf
[locale.conf]: https://github.com/calamares/calamares/blob/master/src/modules/locale/locale.conf
[partition.conf]: https://github.com/calamares/calamares/blob/master/src/modules/partition/partition.conf

### Calamares Settings

At a high level, the `settings.conf` file defines a **sequence** of things
to do (actions) during an installation. This defines the order in which
user-visible actions are taken (e.g. configuring the timezone) as well as
internal actions (e.g. installing the bootloader).

For internal actions, there are different kinds of Calamares modules
which differ in how they are configured. These can be used to run
commands or perform changes to the target system during installation:
 - Always executing one command (*process* modules). Using this
   means creating a new module directory under `$USC/modules` with a
   suitable `module.desc` file that defines the command to run. This
   is no longer recommended.
 - Always executing one or more commands (*shellprocess* instances).
   Using this means defining an instance in `settings.conf` and
   adding a suitable configuration file with the list of commands
   to the configuration directory, e.g. to `$USC/modules/shellprocess`.
   This has the (slight) advantage that it does require intermediate
   directories, and has better error handling.
 - Conditionally executing one or more commands (*contextualprocess*
   instances). This needs an instance and a configuration file
   describing which conditions (expressed as values in the Calamares
   global configuration) cause which commands to run.
 - Conditionally executing one or more commands (*python* job)
   with arbitrary logic. This means creating a new module directory
   under `$USC/modules` and writing a `main.py`. This allows much
   more expressive logic than *contextualprocess* instances, at
   the expense of more complicated packaging and programming.
   Most existing Calamares modules are of this type.
 - Conditionally executing one or more commands (*C++* job)
   with arbitrary logic. This means creating a new module directory
   in the Calamares source tree and adding suitable C++ and CMake
   code to get it to compile. This is the most programmer-intensive
   form of modules, but does allow the most access to Calamares
   internals.

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
