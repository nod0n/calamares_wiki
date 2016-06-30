# LUKS deployment

See article: [LUKS deployment advice](LUKS-Deployment).

# e2fsprogs 1.42.12 dumpe2fs segfault

There is critical issue involving KPMcore and e2fsprogs which breaks partitioning.

Calamares uses KPMcore for partitioning, which in turn queries certain programs in the e2fsprogs package to support filesystem management operations with ext2, ext3 and ext4.

Unfortunately, `dumpe2fs` from e2fsprogs 1.42.12 can segfault when called with no arguments. This is not expected behavior, and it causes the Calamares partition management component to report filesystem resize operations as unsupported. Because of this, Calamares disables Alongside install support.

The bug has already been reported upstream, and a fix is available.

The solution is to **package and deploy e2fsprogs 1.42.13 or later**.

# e2fsprogs 1.43 ext4 default features

As of version 1.43 (including 1.43.1 and possibly later), e2fsprogs ships with different default `mke2fs` features for ext4 filesystems compared to 1.42.13 and earlier. These changes include the features `64bit` and `metadata_csum` being enabled in `/etc/mke2fs.conf`. This is not a problem on its own, however some bootloaders (including GRUB2) refuse to boot from a filesystem which was created with those flags enabled (see [this blog post by Rohan Garg](https://kshadeslayer.wordpress.com/2016/04/11/my-filesystem-has-too-many-bits/) for more information).

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

# systemd automount generators

As of late 2014, systemd supports automounting, presumably with the goal of superseding `fstab`. Regardless of one's opinion on whether systemd should or should not replace certain system components, this behavior of systemd has been found to break Calamares in some occasions.

Namely, in some situations systemd keeps mounting swap partitions it finds (`swapon`), even when the user (or Calamares) runs `swapoff` to unmount a swap partition. For more information, see [Swap Activation by systemd on the Arch Wiki](https://wiki.archlinux.org/index.php/swap#Activation_by_systemd). This inconsistently breaks Calamares partitioning: Calamares needs to disable all swap before performing partitioning operations on that disk, but then systemd immediately turns swap partitions back on, which makes partitioning operations fail. For best results, Calamares must have complete control over the mounted status of all partitions. Any interference might lead to unpredictable behavior.

The specific culprits are `systemd-fstab-generator` and `systemd-gpt-auto-generator`, both in `/usr/lib/systemd/system-generators`.

The first one, `systemd-fstab-generator`, looks for swap partitions in `/etc/fstab` and keeps quickly remounting them when the user unmounts them. To avoid this, simply **make sure that `/etc/fstab` contains no swap partitions** on the live system.

The second one, `systemd-gpt-auto-generator`, only works on GPT partition tables. It monitors all the disks with a GPT disklabel and promptly performs a `swapon` on any partition of type 82 (Linux swap) it can find. It can be disabled by **replacing `/usr/lib/systemd/system-generators/systemd-gpt-auto-generator` with a symlink to `/dev/null`** (very ugly, but apparently that's someone's idea of making it configurable, see [bug report](https://bugs.freedesktop.org/show_bug.cgi?id=87230), [commit](https://cgit.freedesktop.org/systemd/systemd/commit/?id=e801700e9a) and [documentation](https://www.freedesktop.org/software/systemd/man/systemd-gpt-auto-generator.html)). If you test this symlink solution on a running system, don't forget to `systemctl daemon-reload`.

Please note that this systemd peculiarity only affects the live system. On the target system, you are free to configure systemd however you like with no impact on Calamares.