# OEM

OEM modes in Calamares can distinguish configurations for two different
purposes: *pre-delivery* (also called phase-0) is used to prepare 
machine images which are then flashed / written to a device at
the OEM, before delivery to a customer. Phase-1, *post-delivery*,
is used by the customer of an OEM to complete setup. Both phases
are described below (but phase-0 isn't implemented at all).

## General Configuration

 - In `settings.conf`, set *dont-chroot* to *true* to work in the
   current system, as opposed to a target system on another disk.
 - In `settings.conf`, optionally set *disable-cancel* to *true*
   to force the user to continue Calamares operation to completion.

## OEM Imaging (pre-delivery)

> This is about setting up a sensible configuration for Calamares
> so that it writes an OS image with an autologin user.

This is a desirable feature, but not yet planned.

## OEM Configuration (post-delivery)

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
