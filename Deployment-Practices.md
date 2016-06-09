# e2fsprogs

There is critical issue involving KPMcore and e2fsprogs which breaks partitioning.

Calamares uses KPMcore for partitioning, which in turn queries certain programs in the e2fsprogs package to support filesystem management operations with ext2, ext3 and ext4.

Unfortunately, `dumpe2fs` from e2fsprogs 1.42.12 can segfault when called with no arguments. This is not expected behavior, and it causes the Calamares partition management component to report filesystem resize operations as unsupported. Because of this, Calamares disables Alongside install support.

The bug has already been reported upstream, and a fix is available.

The solution is to package and deploy e2fsprogs 1.42.13 or later.

# systemd automount generators

As of late 2014, systemd supports automounting, presumably with the goal of superseding `fstab`. Regardless of one's opinion on whether systemd should or should not replace certain system components, this behavior of systemd has been found to break Calamares in some occasions.

Namely, in some situations systemd keeps mounting swap partitions it finds (`swapon`), even when the user (or Calamares) runs `swapoff` to unmount a swap partition. For more information, see [Swap Activation by systemd on the Arch Wiki](https://wiki.archlinux.org/index.php/swap#Activation_by_systemd). This inconsistently breaks Calamares partitioning: Calamares needs to disable all swap before performing partitioning operations on that disk, but then systemd immediately turns swap partitions back on, which makes partitioning operations fail. For best results, Calamares must have complete control over the mounted status of all partitions. Any interference might lead to unpredictable behavior.

The specific culprits are `systemd-fstab-generator` and `systemd-gpt-auto-generator`, both in `/usr/lib/systemd/system-generators`. The first one looks for swap partitions in `/etc/fstab` and keeps quickly remounting them when the user unmounts them. To avoid this, simply make sure that `/etc/fstab` contains no swap partitions on the live system. The second one only works on GPT partition tables, and allegedly only runs on the root disk (unconfirmed). It can be disabled by replacing it with a symlink to `/dev/null` (very ugly, but apparently that's someone's idea of making it configurable, see [bug report](https://bugs.freedesktop.org/show_bug.cgi?id=87230), [commit](https://cgit.freedesktop.org/systemd/systemd/commit/?id=e801700e9a) and [documentation](https://www.freedesktop.org/software/systemd/man/systemd-gpt-auto-generator.html)).

Please note that this systemd peculiarity only affects the live system. On the target system, you are free to configure systemd however you like with no impact on Calamares.