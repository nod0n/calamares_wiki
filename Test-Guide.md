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

#### KDE Neon VirtualBox Testing

 - Boot a (recent-ish) KDE neon ISO image in VirtualBox
 - In the live system, get *deploycala* and run it:
   ```
   curl -o dp.py https://calamares.io/deploycala.py
   python3 dp.py -N
   ```
   You will need considerable disk space in the live system (i.e. give it
   lots of RAM for tmpfs) to install all of the build requirements
   and then build calamares.
 - After the build, directory `~/calamares` contains the source checkout and
   `~/calamares/build` contains the build itself. Go into the
   build directory and build again, to be sure:
   ```
   cd ~/calamares/build
   make -j4
   ```
 - Since this is a live system, you can `sudo` to install the
   newly-built Calamares; on KDE neon, this ends up in
   "the right places". However, the configuration for Calamares
   for the live image is **not** in the normal places where Calamares looks.
   So we need to install, then set environment variables,
   and then run Calamares
   with the `-X` flag (to use the XDG variables). It's convenient to
   use `-D6` as well for extensive debug logging:
   ```
   sudo make install
   export XDG_DATA_DIRS=/usr/share/calamares:/calamares/desktop
   export XDG_CONFIG_DIRS=/calamares/desktop
   sudo calamares -X -D6
   ```

## Acceptance Test

## Single-module tests

Calamares can be run from the build directory with a local configuration.
Configure and build Calamares normally. Then copy in all of the configuration
files for your distribution into the build directory.
 - `settings.conf` goes in the top-level build directory,
 - configuration files for each module go in the `src/modules/<module>/`
   directory under the build (this mirrors the structure under the top-level
   source directory, too).
If you have a fork of Calamares with the module configurations edited
in the source-tree, the build will have copied all but `settings.conf`
into the appropriate location.

From the build-directory, run Calamares with the *-d* option; you can run
it as a regular user, through sudo, or with pkexec, depending on what you
want to test and how your distribution is set up.
```
    $ ./calamares -d  # As regular user
```

## Standalone module tests

You can test a single module **outside** of Calamares, without
running Calamares at all, with the `loadmodule` test-executable.
For this, you will have to have [built Calamares](Develop-Guide).
Go to the build directory (the regular instructions put that
at `build/` in the source directory).

To load a single module without a UI, use:
```
    $ ./loadmodule <modulename>
```
Fill in the name of the module to test instead of `<modulename>`,
e.g. *dummycpp*. The jobs associated with the module are run, in order,
as if Calamares had listed that module in the *exec* section of
the configuration. Watch out for side effects!

To load a single module with a UI, use:
```
    $ ./loadmodule --ui <modulename>
```
The usual module interface is shown as if the module was listed in
the *show* section of the configuration. The window might not be exactly
the same size as usual, since the navigation controls are missing.

Command-line options `-g <filename>` and `-j <filename>` can feed the module a specific
global configuration or a specific job configuration.

Use `-l <lang>` to specify a language to start the module in, if
it should be different from the system language. This can be helpful
when checking translations.

Use `-b <dir>/branding.desc` to set a different branding directory. This can be helpful
when testing new branding styles.

## Testing the slide show

Developing the slideshow which is shown during the (long, slow) installation
step can be tedious. You need to start, stop, restart and run through the
installation many times. Clicking next, next through the keyboard and locale
configurations becomes increasingly annoying. By changing the Calamares
configuration you can speed up the process considerably.

### Slide show sources

The slide show lives in the branding module. The default slide show is in
`src/branding/default/show.qml`, and it uses QML support files from
`src/qml/calamares/slideshow/`.

When editing the slide show, make a copy to your own branding directory,
and remember to keep editing the file there. Copy the file into the build
directory for testing.

### Standalone slide show tests

The `loadmodule` test-executable can also display the slideshow,
with some fake jobs running while the slideshow is displayed.
Use the `-s` flag to run the slideshow:
```
    $ ./loadmodule -s
```
The `-l` and `-b` options affect branding and language as described
above under [standalone module tests](#standalone-module-tests).
The `-j` flag is not useful because there are no (non-fake) jobs to configure.

The `-g` flag can be used to set up specific fake jobs with a fixed duration
through global settings;
if no global settings are provided, nine (9) fake jobs are added with
varying durations, suitable for seeing through a slideshow of up to a minute.

To create custom fake-job timing for the slideshow, create a file
for the global settings (e.g. `slideshow.global`) and then pass
that with the `-g` flag:
```
    $ ./loadmodule -g slideshow.global -s
```
Bear in mind that the actual **slideshow** settings come from the
branding directory; these slideshow settings are just for the jobs
and the timing.

The format for a fake-jobs global file is:
```
---
jobs:
    - name: first job name
      delay: 2
    - name: next job
      delay: 22
```
Additional global data maybe specified as well, but the important thing
is that *jobs* must be a list, and each item in the list needs a *name*
(which is the string that will be displayed while that job is running)
and a *delay* (in seconds, for how long the job should take). A delay of 0
is legal and will result in an instantaneous job.

### Reduced configuration

For testing the slideshow, all you're really interested in is the slideshow
itself. So reduce the modules used by Calamares to a minimum. In
`settings.conf`, in the build directory, reduce the *sequence* configuration
to the following:
```
    sequence:
    - show:
      - welcome
    - exec:
      - dummypython
    - show:
      - finished
```

Optionally, modify `src/modules/welcome/welcome.conf` (in the build directory!)
to not require anything -- you can run the slide show as a regular user.

Modify the *dummypython* module so that it takes longer. In
`src/modules/dummypython/` (again, in the build directory) the configuration
file `dummypython.conf` contains a key *a_list*, with a list of items. Each item
takes one second to process by the dummy Python module, so add a dozen or so
more items to the list to give your slide show 12 more seconds to run. For
example:

```
    a_list:
      - "item1"
      - "item2"
      - "third"
      - "later"
      # And plenty more
```

Now run `./calamares -d` from the build directory. You should have only a single
step, to click *next* on the welcome page, before the slide show starts running.
Once it does, you can see progress reported by the dummy Python module.
Afterwards, the finished step is displayed, with no change to the system at all.
