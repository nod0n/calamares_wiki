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

Calamares needs Qt5 development headers, KPMcore development headers,
YAML-CPP development headers, Python libraries and development headers, 
Boost::Python libraries and development headers, and more.

Seriously, use `deploycala.py`. If your distro is delivered without
Python3 (e.g. BSD derivatives), use `deploycala-bsd.sh` from roughly
the same URL.


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

When running Calamares with the `-d` parameter, it will pick up a
`settings.conf` placed in the `build` directory (if present), and it will 
show the debug information interface in the bottom left area of the main window.
In a system with no Calamares configuration installed (e.g. a development
VM, or a live CD which doesn't use Calamares yet), you will need to copy
`settings.conf` from the top-level source directory into the build directory.

The source comes with configuration files that are a bit silly, but serve
to demonstrate the whole system. You can copy the example `settings.conf`
into the build-directory:
```
$ cp ../settings.conf .
```


## Supported variables for CMake

There are many CMake-level options that influence the Calamares
build. Most general are the CMake- and KDE Extra-CMake-Modules
variables, 

* `CMAKE_BUILD_TYPE` influences build flags.
* `CMAKE_INSTALL_PREFIX` can be set to a prefix where Calamares and its
  tools will be installed. By default, Calamares follows the GNU InstallDirs
  conventions.
* `KDE_INSTALL_USE_QT_SYS_PATHS` if set to true, prefers to install to
  Qt paths rather than system paths.

Calamares-specific variables can be divided into three categories:

* `WITH_*` specifies whether (optional) dependencies will be used.
* `BUILD_*` specifies what additional parts to build (in particular,
  testing).
* `DEBUG_*` turns on specific and annoying debugging for parts of
  the application.

Calamares has nearly 50 modules. Not all of them are necessary.
Those that have missing dependencies will not be built, but
you can skip modules with two mechanisms:

* `SKIP_MODULES` takes a space-separated list of module names that should 
  not be built even if present in `src/modules`. Default is empty.
  For example,  `cmake -DSKIP_MODULES="partition mount umount welcome" ..`
  builds Calamares without partitioning, mounting or the welcome module.
* `USE_*` when multiple modules exist that are basically incompatible,
  like the services-systemd module and the services-openrc module (no
  installation will have both systemd and openrc), the `USE_*` variables
  can select one of them; this is added to the `SKIP_MODULES` setting.
  For instance, `USE_services=systemd` will add all **other** services-
  modules to `SKIP_MODULES`.

Some modules automatically disable themselves if their dependencies
are not found. Disabled modules are listed at the end of CMake's output.

The **definitive documentation** of CMake variables is at the top
of [CMakeLists.txt](https://github.com/calamares/calamares/blob/master/CMakeLists.txt).


# Quick deployment script

The Calamares team maintains a quick and dirty deployment script, called 
unceremoniously **`deploycala.py`**.

This tool allows everyone to test the current state in any branch in the Calamares GitHub repository without having to manually build and/or repackage Calamares. It relies on an already existing and installed Calamares instance with all its dependencies, making it suitable for testing on a running Live system.

**WARNING**: `deploycala.py` writes into `/usr` and `/etc` with impunity and
no regard for package managers. Its only purpose is to quickly set up a 
Calamares testing and debugging environment on a live system immediately after 
booting from a live medium.<br>Keeping a long term working system is **not** 
a design goal.

Setting up a permanent development environment is **not** a design goal.

**Don't EVER run `deploycala.py` on a non-live system.**

The tool is permanently hosted on ``calamares.io`` for convenience. It is very easy to get it up and running on a live system:
```
$ curl -LO https://calamares.io/deploycala.py
$ python3 deploycala.py
```

## Script usage

The script has a number of options and flags. Run it with the `--help`
or `-h` flags to see a description.

By default, `deploycala.py` updates all the packages on the system and 
builds the master branch. I find myself mostly using it like this:
```
$ python3 deploycala.py -n -N
```

The `-N` flag avoids downloading the script again (it auto-updates) and
`-n` avoids doing a complete system update. This is a good combination
to use with an up-to-date live CD.

#### The good

* On startup, it automatically updates itself from `calamares.io` before 
  building, unless the `-N` flag is given.
* Automatic detection of CPU core count for parallel make.
* It checks out Calamares into a subdirectory `calamares/` in the current 
  directory. If the dir already exists, it will do a pull-rebase instead 
  of a full clone.
* Every build is a fresh build (unless you use `-i`).
* Submodules are supported.
* It will set up distributed builds with `icecream` if the relevant 
  package can be found.
* It will also set up `sudo-gdb`, Qt Creator and some other IDE configuration 
  files if the `--full-ide` flag is given.

#### The bad

* It only supports a limited set of package managers for dependency 
  installation, and contains a best-guess of the names of the 
  development-packages needed for Calamares. It is tested to work on 
  Chakra Linux, Netrunner Rolling, Manjaro KDE and KaOS. 
  Pull requests for other package managers are accepted.
* It backs up `/usr/share/calamares` and `/etc/calamares` as a whole, so
  inevitably upstream changes in configuration and/or branding format
  might break things. Caveat emptor.
* It is noninteractive only as long as `sudo` is configured as `NOPASSWD` for the current user.

#### The ugly

* It's a Python script that happily writes into / and overwrites files owned
  by the package manager. It's been used successfully on Chakra Linux,
  Netrunner Rolling, Manjaro KDE and KaOS live systems to test changes
  immediately after pushing. Its purpose is **not** deployment for the end-user,
  but shortening a developer's code-build-push-test iteration.
* It **will** happily and mercilessly break your system in various ways if you
  try to use it in any way beyond what's outlined above.
* It is released in the hope that it might make your system integration tasks
  easier as well, but **without any warranty**.


# Additional developer documentation

In the wiki:
* [Coding Style](Develop-Code)
* [Design Notes](Develop-Design)
* [Global Storage reference](Develop-GlobalStorage)

In the source:
* [astyle configuration](https://github.com/calamares/calamares/blob/master/ci/astylerc)
* [clang-format](https://github.com/calamares/calamares/blob/master/.clang-format)
* [RELEASE instructions](https://github.com/calamares/calamares/blob/master/ci/RELEASE.md)
  and [script](https://github.com/calamares/calamares/blob/master/ci/RELEASE.sh)
* [Module descriptors](https://github.com/calamares/calamares/blob/master/src/modules/README.md)
