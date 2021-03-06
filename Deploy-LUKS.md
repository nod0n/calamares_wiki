# Dependencies

In order for the full disk encryption feature to work, the following is needed on the live system:
* Calamares 2.3 or later (depending on your initramfs generator, a more recent version may be required, see the instructions below);
* KPMcore 2.2 or later;
* `cryptsetup` 1.7 or later;
* GRUB 2 (2.02-beta2 known to work) - see warning below.

Additionally, the following must be present inside the image that ends up as your rootfs:
* an initramfs management solution, like `mkinitcpio` (see the instructions below for a list of currently supported ones);
* in order to support resume from suspend-to-disk with encrypted swap, a custom initramfs hook that will unlock the swap partition early, like [`mkinitcpio-openswap`](https://aur.archlinux.org/packages/mkinitcpio-openswap/) (unless this is handled directly by your initramfs management solution).

**WARNING:** while the Calamares bootloader module supports systemd-boot (formerly gummiboot), it does not support systemd-boot with the full disk encryption feature. Unlike GRUB 2, systemd-boot cannot perform boot loader encryption (i.e. early disk unlocking on boot loader startup), making *full* disk encryption impossible and potentially allowing for an additional attack vector. Some distributions report that LUKS installs work even with systemd-boot, and this is fine, but this approach requires additional deployment effort and may result in a less secure system. For this reason, the Calamares team does not recommend it.

# Configuring Calamares for full disk encryption

Check whether you have all the dependencies listed above (as well as all the usual Calamares dependencies), then proceed as described below, depending on your initramfs management system:

## `mkinitcpio` (e.g., Arch Linux)

If your system has `mkinitcpio`:

* use Calamares 2.3 or later,
* deploy the `mkinitcpio-openswap` package to your rootfs image,
* uncomment the `luksbootkeyfile` and `luksopenswaphookcfg` modules in `settings.conf`.
* uncomment the `grubcfg` modules in `settings.conf`.
* add `GRUB_ENABLE_CRYPTODISK: true` to the `defaults` section of `grubcfg.conf`.

## `update-initramfs` from `initramfs-tools` (Debian and derivatives)

If your system has `update-initramfs` from the Debian `initramfs-tools`:

* use Calamares 2.4.3 or later,
* uncomment the `luksbootkeyfile` module in `settings.conf`,
* add the `initramfscfg` module to `settings.conf` (after `mount`, but before `initramfs`),
* uncomment the `grubcfg` modules in `settings.conf`.
* add `GRUB_ENABLE_CRYPTODISK: true` to the `defaults` section of `grubcfg.conf`.
* in `fstab.conf`, change the `crypttabOptions` line as documented in the comments in the file (the `initramfs-tools` will otherwise completely ignore the keyfile), i.e., to:
```yaml
crypttabOptions: luks,keyscript=/bin/cat
```
* in Debian-live-based distros, configure the `packages` module to remove `^live-*` packages so that `update-initramfs` can run properly, otherwise the initramfs hook will never run. The following packages.conf should suffice:
```yaml
backend: apt

operations:
    - remove:
        - '^live-*'
```
* unlocking swap on resume is **not** supported yet.

## `dracut` (e.g., Fedora, OpenSUSE)

If your system has the `dracut` initramfs management system:

* use Calamares 2.4.3 or later,
* uncomment the `luksbootkeyfile` and `dracutlukscfg` modules in `settings.conf`.
* uncomment the `grubcfg` modules in `settings.conf`.
* add `GRUB_ENABLE_CRYPTODISK: true` to the `defaults` section of `grubcfg.conf`.

Caveats:
* We were unable to test resuming from encrypted swap because resuming from hibernation did not even work for us in the unencrypted case. There should be no special hook (such as `mkinitcpio-openswap`) needed with `dracut`, and we already write the required configuration setting, but it is currently **not** working for us because resuming was just generally broken in our testing.
* With older versions of `systemd`, there may be issues with multiple encrypted partitions (i.e., if you use manual partitioning and create separate partitions, e.g., for `/` and `/home`). We know that `systemd` 226 does the right thing (as do the newer versions that Fedora ships), whereas `systemd` 216 somehow interprets the `rd.luks.uuid` parameters (intended only for `dracut`) for itself (contrary to what the documentation states). We have not tested intermediate versions. The result is that with such old `systemd` versions, the partitions beyond `/` (e.g., the `/home` partition) are not automatically unlocked using the keyfile and you eventually get prompted for a passphrase. This is fixed in more recent versions of `systemd` (at least 226 or later), so upgrading is strongly recommended.
* You may also be running into issues (especially, but not necessarily, with multiple encrypted partitions) if you use the `hostonly="no"` setting. We tried hard to support this mode, and it worked in our tests, but the `hostonly="no"` mode is not designed by the `dracut` developers to support system-specific configuration (such as `/etc/crypttab`), so we are actually working around the intended purpose of that mode. So use it at your own risk.

## Other initramfs management systems

Unfortunately, if you are not using one of the above, already supported initramfs management systems, there is some more deployment work to do on your end. You will probably want to read the technical details below, but the basic steps are:

* Be sure to **add the `luksbootkeyfile` module**, which is common to all initramfs management systems, to your main `exec` phase in `settings.conf`, after `mount` but before whatever module you are using to set up your initramfs configuration and build the image.
* You will need to configure your initramfs generator to include the keyfile `/crypto_keyfile.bin` (written by the above) in the initramfs. You may also have to explicitly tell it to enable LUKS support and/or to include `/etc/crypttab`. (See the `mkinitcpiocfg`, `initramfscfg` and `dracutlukscfg` modules for examples of how this is done in different initramfs management systems.)
* For resuming from an encrypted swap partition to work, you will probably need a hook such as `mkinitcpio-openswap` and to enable it in the configuration (as the `luksopenswaphookcfg` module does for `mkinitcpio-openswap`). This is highly dependent on the individual initramfs management system, so please refer to its documentation.
* uncomment the `grubcfg` modules in `settings.conf`.
* add `GRUB_ENABLE_CRYPTODISK: true` to the `defaults` section of `grubcfg.conf`.

# Technical details about full disk encryption support in Calamares

The following modules are used for LUKS full disk encryption support:

## `luksbootkeyfile`

* Used with: all initramfs generators

This module creates a LUKS keyfile in the rootfs and registers it with all LUKS devices that were set up in the current Calamares session. The keyfile is then integrated into the initramfs by the `initcpio` (or equivalent) module, and is then used by GRUB to unlock the root and swap partitions.

Please note that this approach is secure and comfortable as long as the initramfs image lives on an encrypted partition, and it is made possible by the GRUB 2 boot loader encryption feature - GRUB 2 prompts for the passphrase once and never again, because further unlocking is done with the keyfile.

If you use the `initcpiocfg` and `initcpio` modules, or the `initramfscfg` and `initramfs` modules, or the `dracutlukscfg` and `dracut` modules, shipped with Calamares, no further action is needed to set up the keyfile. If you use a custom module instead of `initcpiocfg`/`initcpio`, make sure that your solution copies the file `/crypto_keyfile.bin` (previously created by `luksbootkeyfile`) into your initramfs.

## `luksopenswaphookcfg`

* Used with: `mkinitcpio`

Calamares ships the `luksopenswaphookcfg` module, whose goal is to write the configuration file for the `openswap` hook for `mkinitcpio`. Unfortunately, this bit is distribution-specific, and it will only work on systems that use `mkinitcpio` (mostly Arch Linux related systems).

If you have such a system, simply **add the `luksopenswaphookcfg` module** to your main `exec` phase in `settings.conf`, after `mount` but before `initcpiocfg`/`initcpio`. In this case, no further action is needed (besides making sure that `mkinitcpio-openswap` is in fact available on your target rootfs image).

If your system does not use `mkinitcpio`, you will need to roll out a custom solution. In this case, the Calamares team would be happy to assist and accept pull requests to merge such a solution upsteam (feel free to reach out to us in #calamares on irc.freenode.net). Generally, what you need to provide is:

1. a hook or script for your initramfs builder mechanism, with the goal of LUKS-unlocking inside the initramfs not just the root partition but also the swap partition, using the keyfile that `luksbootkeyfile` generated and that you had previously told your initramfs builder to bake into the initramfs (this component is `mkinitcpio-openswap` in the `mkinitcpio` world);

2. a Calamares module, likely written in Python, or possibly even just a patch for the existing `luksopenswaphookcfg` module, that will write the relevant configuration file which tells your (1) initramfs hook or script where to find the swap partition, how to call it once it's unlocked, and how to unlock it.

See [Swap encryption on Arch Wiki](https://wiki.archlinux.org/index.php/Dm-crypt/Swap_encryption#mkinitcpio_hook) for more information on early swap partition unlocking techniques.

## `initramfscfg`

* Used with: Debian `initramfs-tools`

This module handles the pre-configuration of the `update-initramfs` tool (which is then actually run in the `initramfs` module). When LUKS full disk encryption is enabled, this module will install an `initramfs-tools` hook that copies the keyfile from the `luksbootkeyfile` module and the `/etc/crypttab` file into the initramfs that will be generated by `update-initramfs`.

The caveat to using this module successfully in Debian-live-based distros is that the `packages` module must be configured to removed `^live-*` packages so that `update-initramfs` can run properly, otherwise the initramfs hook will never run. (See the configuration instructions above.)

Note that this module does not currently handle swap unlocking on resume. It could either be added to this module or implemented as a separate module like `luksopenswaphookcfg` above for `mkinitcpio-openswap`.

## `dracutlukscfg`

* Used with: `dracut`

This module handles the pre-configuration of the `dracut` tool (which is then actually run in the `dracut` module) for LUKS full disk encryption. When LUKS full disk encryption is enabled, this module will install a `/etc/dracut.conf.d/calamares-luks.conf` file with the following configuration settings:
* `install_items+=" /etc/crypttab /crypto_keyfile.bin "`
  * forces including `/etc/crypttab` in the initramfs even if `hostonly="no"`. If `hostonly="yes"` (the default), `dracut` automatically installs a filtered version of `/etc/crypttab` with exactly the devices it needs, and ignores the `install_items` entry for it. But if `hostonly="no"`, `dracut` does not install any host-specific configuration that was not explicitly requested to be included. (In that case, the entire file is included, but it should not hurt.)
  * includes the keyfile `/crypto_keyfile.bin` that was written by the `luksbootkeyfile` module in the initramfs.
* `add_device+=" /dev/disk/by-uuid/%1 "` (where `%1` is the UUID of the encrypted swap partition)
  * unlocks the swap device in the initramfs so that resuming from it should theoretically work. Unfortunately, we were unable to test this because resuming did **not** work for us, even in the unencrypted case.
