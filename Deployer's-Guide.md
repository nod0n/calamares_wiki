# Deployer's Guide

> Calamares is designer to be thoroughly configurable and suitable for
> a wide range of use cases, but it is not an end-user application,
> nor is its configuration something end-users should be concerned
> about. Deployers of Calamares -- that is, distro's and OEMs who use
> Calamares for system installation -- should be configuring
> the application.

* [Working with modules](https://github.com/calamares/calamares/blob/master/src/modules/README.md) (in master, TODO: move to wiki)
* [Setting up branding](https://github.com/calamares/calamares/blob/master/src/branding/README.md) (in master, TODO: move to wiki)
* [Deployment Practices](Deployment-Practices)

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
the respective files.


| File name | Deployment path (in search priority) | Purpose |
|:---------:|:------------------------------------:|:-------:|
| [`settings.conf`][settings.conf] | `/etc/calamares`, `/usr/share/calamares` | Main Calamares configuration file |
| _`modulename`_`.conf`, e.g. `partition.conf`, `grubcfg.conf`, `unpackfs.conf`, ... | `/etc/calamares/modules`, `/usr/share/calamares/modules` | Configuration files for every module that needs one |
| [`partition.conf`][partition.conf] | | Configuration for (automatic) partitioning and swap |
| [`displaymanager.conf`][displaymanager.conf] | | Configuration for the display manager, select SDDM, lightdm, ... |
| [`branding.desc`][branding.desc] | `/usr/share/calamares/`_`branding_component_name`_ | Branding descriptor file, shipped with the rest of your branding component and selected in `settings.conf` |

[settings.conf]: https://github.com/calamares/calamares/blob/master/settings.conf
[branding.desc]: https://github.com/calamares/calamares/blob/master/src/branding/default/branding.desc
[displaymanager.conf]: https://github.com/calamares/calamares/blob/master/src/modules/displaymanager/displaymanager.conf
[partition.conf]: https://github.com/calamares/calamares/blob/master/src/modules/partition/partition.conf

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

> This isa bout the configuration of Calamares that should be
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

As of late 2014, systemd supports automounting, presumably with the goal of superseding `fstab`. Regardless of one's opinion on whether systemd should or should not replace certain system components, this behavior of systemd has been found to break Calamares in some occasions.

Namely, in some situations systemd keeps mounting swap partitions it finds (`swapon`), even when the user (or Calamares) runs `swapoff` to unmount a swap partition. For more information, see [Swap Activation by systemd on the Arch Wiki](https://wiki.archlinux.org/index.php/swap#Activation_by_systemd). This inconsistently breaks Calamares partitioning: Calamares needs to disable all swap before performing partitioning operations on that disk, but then systemd immediately turns swap partitions back on, which makes partitioning operations fail. For best results, Calamares must have complete control over the mounted status of all partitions. Any interference might lead to unpredictable behavior.

The specific culprits are `systemd-fstab-generator` and `systemd-gpt-auto-generator`, both in `/usr/lib/systemd/system-generators`.

The first one, `systemd-fstab-generator`, looks for swap partitions in `/etc/fstab` and keeps quickly remounting them when the user unmounts them. To avoid this, simply **make sure that `/etc/fstab` contains no swap partitions** on the live system.

The second one, `systemd-gpt-auto-generator`, only works on GPT partition tables. It monitors all the disks with a GPT disklabel and promptly performs a `swapon` on any partition of type 82 (Linux swap) it can find. It can be disabled by **replacing `/usr/lib/systemd/system-generators/systemd-gpt-auto-generator` with a symlink to `/dev/null`** (very ugly, but apparently that's someone's idea of making it configurable, see [bug report](https://bugs.freedesktop.org/show_bug.cgi?id=87230), [commit](https://cgit.freedesktop.org/systemd/systemd/commit/?id=e801700e9a) and [documentation](https://www.freedesktop.org/software/systemd/man/systemd-gpt-auto-generator.html)). If you test this symlink solution on a running system, don't forget to `systemctl daemon-reload`.

Please note that this systemd peculiarity only affects the live system. On the target system, you are free to configure systemd however you like with no impact on Calamares.


