# Deployer's Guide

> Calamares is designer to be thoroughly configurable and suitable for
> a wide range of use cases, but it is not an end-user application,
> nor is its configuration something end-users should be concerned
> about. Deployers of Calamares -- that is, distro's and OEMs who use
> Calamares for system installation -- should be configuring
> the application.

* [Working with modules](https://github.com/calamares/calamares/blob/master/src/modules/README.md)
* [Setting up branding](https://github.com/calamares/calamares/blob/master/src/branding/README.md)

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

See article: [LUKS deployment advice](LUKS-Deployment).

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

### OEM Imaging (pre-delivery)

> This is about setting up a sensible configuration for Calamares
> so that it writes an OS image with an autologin user.

### OEM Configuration (post-delivery)

> This is about the configuration of Calamares that should be
> written into the image, so that on first boot it can do
> the right thing.

## Known Issues

> These are (historically) documented known "gotchas" when deploying
> Calamares. Typically they are configuration issues and bugs in
> external tools used by Calamares during system installation.

### e2fsprogs 1.42.12 dumpe2fs segfault

There is critical issue involving KPMcore and e2fsprogs which breaks partitioning.
The solution is to **package and deploy e2fsprogs 1.42.13 or later**.

### e2fsprogs 1.43 ext4 default features

As of version 1.43 (including 1.43.1 and possibly later), e2fsprogs ships with different default `mke2fs` features for ext4 filesystems compared to 1.42.13 and earlier. These changes include the features `64bit` and `metadata_csum` being enabled in `/etc/mke2fs.conf`. This is not a problem on its own, however some bootloaders (including GRUB2) refuse to boot from a filesystem which was created with those flags enabled (see [this blog post by Rohan Garg](https://kshadeslayer.wordpress.com/2016/04/11/my-filesystem-has-too-many-bits/) for more information). Also be advised that e2fsck 1.42.13 and earlier fails when checking a filesystem with `metadata_csum` enabled.

To work around this issue, **please update your e2fsprogs 1.43 series package to ship a `mke2fs.conf` file without the features `64bit` and `metadata_csum`**, as it was in 1.42.13 and earlier.

Good:
```
ext4 = {
        features = has_journal,extent,huge_file,flex_bg,uninit_bg,dir_nlink,extra_isize
```
Bad:
```
ext4 = {
        features = has_journal,extent,huge_file,flex_bg,64bit,metadata_csum,dir_nlink,extra_isize
```

This issue will also affect any other installer or even a manual install, unless the application or the user explicitly disables those flags.

### systemd automount generators

As of late 2014, systemd supports automounting, presumably with the goal of
superseding `fstab`. Regardless of one's opinion on whether systemd should or
should not replace certain system components, this behavior of systemd has been
found to break Calamares in some occasions.

Namely, in some situations systemd keeps mounting swap partitions it finds
(`swapon`), even when the user (or Calamares) runs `swapoff` to unmount a swap
partition. For more information, see [Swap Activation by systemd on the Arch
Wiki](https://wiki.archlinux.org/index.php/swap#Activation_by_systemd). This
inconsistently breaks Calamares partitioning: Calamares needs to disable all
swap before performing partitioning operations on that disk, but then systemd
immediately turns swap partitions back on, which makes partitioning operations
fail. For best results, Calamares must have complete control over the mounted
status of all partitions. Any interference might lead to unpredictable behavior.

The specific culprits are `systemd-fstab-generator` and
`systemd-gpt-auto-generator`, both in `/usr/lib/systemd/system-generators`.

The first one, `systemd-fstab-generator`, looks for swap partitions in
`/etc/fstab` and keeps quickly remounting them when the user unmounts them. To
avoid this, simply **make sure that `/etc/fstab` contains no swap partitions**
on the live system.

The second one, `systemd-gpt-auto-generator`, only works on GPT partition
tables. It monitors all the disks with a GPT disklabel and promptly performs a
`swapon` on any partition of type 82 (Linux swap) it can find. It can be
disabled by linking that file to `/dev/null`, either in
`/usr/lib/systemd/system-generators` (invasive) or by adding
a the symlink to `/etc/systemd/system-generators` (less invasive).
This is very ugly, but apparently that's someone's idea of making it
configurable, see
[bug report](https://bugs.freedesktop.org/show_bug.cgi?id=87230),
[commit](https://cgit.freedesktop.org/systemd/systemd/commit/?id=e801700e9a) and
[documentation](https://www.freedesktop.org/software/systemd/man/systemd-gpt-auto-generator.html).
If you test this symlink solution on a running system,
don't forget to `systemctl daemon-reload`.

Please note that this systemd peculiarity only affects the live system. On the
target system, you are free to configure systemd however you like with no impact
on Calamares.


