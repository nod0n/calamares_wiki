
**TL;DR:** if your system has `mkinitcpio`, deploy the `mkinitcpio-openswap` package to your rootfs image, add `luksbootkeyfile` and `luksopenswaphookcfg` to `settings.conf` and you're done. Otherwise, read on because there's some more deployment work to do on your end.

# Dependencies

In order for the full disk encryption feature to work, the following is needed on the live system:
* Calamares 2.3 or later;
* KPMcore 2.2 or later;
* `cryptsetup` 1.7-ish (1.7.2 known to work);
* GRUB 2 (2.02-beta2 known to work) - see warning below.

Additionally, the following must be present inside the image that ends up as your rootfs:
* an initramfs management solution, like `mkinitcpio`;
* in order to support resume from suspend-to-disk with encrypted swap, a custom initramfs hook that will unlock the swap partition early, like `[mkinitcpio-openswap](https://aur.archlinux.org/packages/mkinitcpio-openswap/)`.

**WARNING:** while the Calamares bootloader module supports systemd-boot (formerly gummiboot), it does not support systemd-boot with the full disk encryption feature. Unlike GRUB 2, systemd-boot cannot perform boot loader encryption (i.e. early disk unlocking on boot loader startup), making *full* disk encryption impossible and potentially allowing for an additional attack vector. Some distributions report that LUKS installs work even with systemd-boot, and this is fine, but this approach requires additional deployment effort and may result in a less secure system. For this reason, the Calamares team does not recommend it.

# Configuring Calamares for full disk encryption

Check whether you have all the dependencies listed above (as well as all the usual Calamares dependencies), then proceed as described below.

## `luksbootkeyfile`

Be sure to **add the `luksbootkeyfile` module** to your main `exec` phase in `settings.conf`, after `mount` but before `initcpiocfg`/`initcpio` (or whatever other custom module you might be using to set up your initramfs configuration and build the image). This module creates a LUKS keyfile in the rootfs and registers it with all LUKS devices that were set up in the current Calamares session. The keyfile is then integrated into the initramfs by the `initcpio` module, and is then used by GRUB to unlock the root and swap partitions.

Please note that this approach is secure and comfortable as long as the initramfs image lives on an encrypted partition, and it is made possible by the GRUB 2 boot loader encryption feature - GRUB 2 prompts for the passphrase once and never again, because further unlocking is done with the keyfile.

If you use the `initcpiocfg` and `initcpio` modules shipped with Calamares, no further action is needed to set up the keyfile. If you use a custom module instead of `initcpiocfg`/`initcpio`, make sure that your solution copies the file `/crypto_keyfile.bin` (previously created by `luksbootkeyfile`) into your initramfs.

## `luksopenswaphookcfg`

Calamares ships the `luksopenswaphookcfg` module, whose goal is to write the configuration file for the `openswap` hook for `mkinitcpio`. Unfortunately, this bit is distribution-specific, and it will only work on systems that use `mkinitcpio` (mostly Arch Linux related systems).

If you have such a system, simply **add the `luksopenswaphookcfg` module** to your main `exec` phase in `settings.conf`, after `mount` but before `initcpiocfg`/`initcpio`. In this case, no further action is needed (besides making sure that `mkinitcpio-openswap` is in fact available on your target rootfs image).

If your system does not use `mkinitcpio`, you will need to roll out a custom solution. In this case, the Calamares team would be happy to assist and accept pull requests to merge such a solution upsteam (feel free to reach out to us in #calamares on irc.freenode.net). Generally, what you need to provide is:
1) a hook or script for your initramfs builder mechanism, with the goal of LUKS-unlocking inside the initramfs not just the root partition but also the swap partition, using the keyfile that `luksbootkeyfile` generated and that you had previously told your initramfs builder to bake into the initramfs (this component is `mkinitcpio-openswap` in the `mkinitcpio` world);
2) a Calamares module, likely written in Python, or possibly even just a patch for the existing `luksopenswaphookcfg` module, that will write the relevant configuration file which tells your (1) initramfs hook or script where to find the swap partition, how to call it once it's unlocked, and how to unlock it.
