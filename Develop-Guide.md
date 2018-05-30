> This guide describes how to develop Calamares -- that is,
> compile it, configure those parts that don't fall under
> deployment, and contains some notes on packaging Calamares as well.

# Pre-requisites

Calamares is a C++ program. It uses the Qt libraries. It may use KDE 
Frameworks libraries. You will need those installed on your development
system in order to work on Calamares. You may need additional tools,
such as a text-editor, the git reversion-control system, Qt Designer,
and an image-manipulation program.

See the heading [Quick Deployment Script](#quick-deployment-script)
for information on how to quickly install a development environment on
most **live** CD systems (not recommended for installed systems outside
of development-VMs).

# How to build Calamares

Clone Calamares from GitHub, run CMake, and compile it:
```
$ git clone https://github.com/calamares/calamares.git
$ mkdir calamares/build
$ cd calamares/build
$ cmake -DCMAKE_BUILD_TYPE=Debug ..
$ make
```

See below for a list of CMake variables that influence the Calamares
build and optional components.

This will give you a debug build of Calamares, with debug symbols. 
It can then be run straight from the `build` directory without installing in 
one of the following ways:
```
$ ./calamares -d
$ sudo ./calamares -d
$ pkexec ./calamares -d
```
To start it in `gdb`:
```
$ sudo gdb ./calamares
(gdb) run -d
```
When running Calamares with the `-d` parameter, it will also pick up a 
`settings.conf` placed in the `build` directory (if present), and it will 
show the debug information interface in the bottom left area of the main window.
In a system with no Calamares configuration installed (e.g. a development
VM, or a live CD which doesn't use Calamares yet), you will need to copy
`settings.conf` from the top-level source directory into the build directory.

## Supported variables for CMake

Options that influence what parts of Calamares are built:

* `WITH_KF5Crash` - improved crash reporting, compatible with Dr. Konqui.
  Default is true.
* `WITH_PYTHON` - if this is set to false, the Python module interface will 
  not be built. Default is true. (Python is optional, but strongly recommended)
* `WITH_PYTHONQT` - if this is set to false, the PythonQT module interface will
  bot be built. Default is true. (PythonQt is optional)
* `SKIP_MODULES` - takes a space-separated list of module names that should 
  not be built even if present in `src/modules`. Default is empty.
  For example,  `cmake -DSKIP_MODULES="partition mount umount welcome" ..`
  builds Calamares without partitioning, mounting or the welcome module.
  Some modules automatically disable themselves if their dependencies
  are not found. Disabled modules are listed at the end of CMake's output.
* `BUILD_TESTING` - if this is set to true, then test-applications and tools
  are built, along with the main Calamares executable and modules. Default
  is true. The test-applications are not installed or packaged, and can
  be found in the build directory (for testing purposes).

Options that influence where Calamares is installed:
* `CMAKE_INSTALL_PREFIX` can be set to a prefix where Calamares and its
  tools will be installed. By default, Calamares follows the GNU InstallDirs
  conventions.
* `KDE_INSTALL_USE_QT_SYS_PATHS` if set to true, prefers to install to
  Qt paths rather than system paths.

# Quick deployment script

The Calamares team maintains a quick and dirty deployment script, called uncerimoniously **`deploycala.py`**.

This tool allows everyone to test the current state in any branch in the Calamares GitHub repository without having to manually build and/or repackage Calamares. It relies on an already existing and installed Calamares instance with all its dependencies, making it suitable for testing on a running Live system.

**WARNING**: `deploycala.py` writes into `/usr` and `/etc` with impunity and no regard for package managers. Its only purpose is to quickly set up a Calamares testing and debugging environment on a live system immediately after booting from a live medium.<br>Keeping a long term working system is **not** a design goal.<br>Setting up a permanent development environment is **not** a design goal.<br>**Don't EVER run `deploycala.py` on a non-live system.**

The tool is permanently hosted on ``calamares.io`` for convenience. It is very easy to get it up and running on a live system:
```
$ curl -LO calamares.io/deploycala.py
$ chmod +x deploycala.py
$ ./deploycala.py
```

## Script usage

See
```
$ ./deploycala.py -h
```

By default, `deploycala.py` updates all the packages on the system and builds the master branch. I find myself mostly using it like this:
```
$ ./deploycala.py -ni some_branch
```

#### The good
* On startup, it automatically updates itself from calamares.io before building.
* Automatic detection of CPU core count for parallel make.
* It checks out Calamares into a subdirectory "calamares" in the current directory. If the dir already exists, it will do a pull-rebase instead of a full clone.
* Every build is a fresh build (unless you use `-i`).
* Submodules are supported.
* It will set up distributed builds with `icecream` if the relevant package can be found.
* It will also set up `sudo-gdb`, Qt Creator and some other IDE configuration files.

#### The bad
* It only supports yaourt and pacman for dependency install right now, and is tested to work on Chakra Linux, Netrunner Rolling, Manjaro KDE and KaOS. Pull requests for other package managers are accepted.
* It backs up `/usr/share/calamares` and `/etc/calamares` as a whole, so inevitably upstream changes in configuration and/or branding format might break things. Caveat emptor.
* It is noninteractive only as long as `sudo` is configured as `NOPASSWD` for the current user.

#### The ugly
* It's a Python script that happily writes into / and overwrites files owned by the package manager. It's been used successfully on Chakra Linux, Netrunner Rolling, Manjaro KDE and KaOS live systems to test changes immediately after pushing. Its purpose is **not** deployment for the end-user, but shortening a developer's code-build-push-test iteration.
* It **will** happily and mercilessly break your system in various ways if you try to use it in any way beyond what's outlined above.
* It is released in the hope that it might make your system integration tasks easier as well, but **without any warranty**.

# Additional developer documentation

* [GlobalStorage keys reference](GlobalStorage keys reference)
* [Design Notes](Design-Notes)
* [HACKING file](https://github.com/calamares/calamares/blob/master/HACKING.md) - kept in master, includes code style guidelines, best practices, etc.
* [astyle configuration](https://github.com/calamares/calamares/tree/master/hacking)
* [RELEASE instructions](https://github.com/calamares/calamares/blob/master/hacking/RELEASE.md)
