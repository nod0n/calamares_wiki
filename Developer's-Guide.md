# Setting up a development environment
Clone Calamares from GitHub and `cd` into the calamares directory, then:

```
$ git submodule init
$ git submodule update
$ mkdir build
$ cd build
$ cmake -DCMAKE_BUILD_TYPE=Debug ..
$ make
```

This will give you a debug build of Calamares, with debug symbols. It can then be run straight from the `build` directory without installing in any of the following ways:
```
$ ./calamares -d
$ sudo ./calamares -d
$ pkexec ./calamares -d
```
To start it in `gdb`:
```
$ sudo gdb ./calamares
(gdb) r -d
```
When running Calamares with the `-d` parameter, it will also pick up a `settings.conf` placed in the `build` directory (if present), and it will show the debug information interface in the bottom left area of the main window.

## Supported variables for CMake

* `WITH_PYTHON` - if this is set to false, the Python module interface will not be built. Default is true.
* `SKIP_MODULES` - takes a space-separated list of module names that should not be built even if present in `src/modules` (e.g. `cmake -DSKIP_MODULES="partition mount umount welcome" ..`). Default is empty.

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
* It only supports yaourt and pacman for dependency install right now, and is tested to work on Netrunner Rolling, Manjaro KDE and KaOS. Pull requests for other package managers are accepted.
* It backs up `/usr/share/calamares` and `/etc/calamares` as a whole, so inevitably upstream changes in configuration and/or branding format might break things. Caveat emptor.
* It is noninteractive only as long as `sudo` is configured as `NOPASSWD` for the current user.

#### The ugly
* It's a Python script that happily writes into / and overwrites files owned by the package manager. It's been used successfully on Netrunner Rolling, Manjaro KDE and KaOS live systems to test changes immediately after pushing. Its purpose is **not** deployment for the end-user, but shortening a developer's code-build-push-test iteration.
* It **will** happily and mercilessly break your system in various ways if you try to use it in any way beyond what's outlined above.
* It is released in the hope that it might make your system integration tasks easier as well, but **without any warranty**.

# Additional developer documentation

* [GlobalStorage keys reference](GlobalStorage keys reference)
* [Design Notes](Design-Notes)
* [HACKING file](https://github.com/calamares/calamares/blob/master/HACKING.md) - kept in master, includes code style guidelines, best practices, etc.
* [astyle configuration](https://github.com/calamares/calamares/tree/master/hacking)

