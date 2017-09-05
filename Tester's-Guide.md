# Tester's Guide

> This section of the wiki is about **testing** Calamares; it details scenario's that should
> be checked during acceptance testing, but also gives tips on how to configure systems (both
> physical and virtual) for testing Calamares.

## Test System Setup

A system suitable for testing Calamares need not be an expensive physical machine. A virtual machine (VM) will do.

### VirtualBox Setup

VirtualBox is a free, largely Free Software, virtual machine system with excellent support for multiple Linux systems with full graphical desktops running on a host. Setting up VirtualBox in general on the host system is outside the scope of this wiki.

When configuring virtual machines for use in testing Calamares,

* Give the VM at least 1GB of RAM; this is because most Calamares configurations have a requirements-check for 1GB of RAM or more. 4GB is recommended, for a reasonable system.
*  Give the VM at least 8GB of disk space; the default Calamares configuration wants 5.5GB, and most live-ISO systems with a graphical desktop will use that much. 20GB is recommended -- that is small enough not to overload the host system with useless virtual disks, but large enough to allow experimentation with side-by-side installs, shared swap space, etc.

You can configure the ssystem as a BIOS, or as an EFI machine under *Settings* -> *System* -> *Motherboard*, with the checkbox *Enable EFI*. Calamares should detect the difference automatically.

#### VirtualBox USB Pass-through

USB pass-through provides a USB device which is plugged in to the host system to the guest system as a local USB device. This allows you to, for instance, install with Calamares onto a physical USB stick, which can then be used to 
boot a physical machine.

> This mini-HOWTO is thanks to @abucodonosor in #698

* First download Oracle VM VirtualBox Extension Pack matching your VirtuaBox version and install it on the host. Check your user is in group vboxusers. You'll need to reboot to load the VirtualBox drivers (or mess around with re-loading kernel modules).
* Now fire up Vbox GUI and create some VM 
   - click the VM , Settings -> System and enable EFI
   - Settings -> USB -> Enable USB 2.0 or USB 3.0 (whatever you are using)
   - _do not add USB devices here do it from GUEST or using command line _  
* Plug in some USB stick on HOST
* Fire up the VM (with an ISO that supports at least UEFI booting)
  - on GUEST now -> Devices -> USB (add the USB stick or devices you need)
  - on HOST, wait until GUEST is up and run
      ```
           VBoxManage list usbhost
      ```
    then look for the UUID of the USB device you want to attach to GUEST
      ```
           VBoxManage controlvm <VM_NAME> usbattach <THE_UUID>
      ```


## Acceptance Test