# Building
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

# Developer documentation

* [Design Notes](Design-Notes)
* [HACKING file](https://github.com/calamares/calamares/blob/master/HACKING.md) - kept in master, includes code style guidelines, best practices, etc.

