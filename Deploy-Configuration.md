# Calamares Configuration

Each sample configuration file contains documentation on the options it
contains (it not, it's a bug and you should file an issue).

The top-level configuration of Calamares is done in
[`settings.conf`][settings.conf], the file which defines which modules
the system uses and whether it is in OEM (*dont-chroot*) or normal
system-installer mode. The modules listed in `settings.conf`
are loaded one by one; their configurations are documented in
the respective files (see the [source tree][modules]).

## Recommendations

Distributions are **strongly urged** to package configuration files
separately from Calamares itself, and to install them in `/etc/calamares`.
It is **not recommended** to edit or patch the configuration files
included with Calamares for production use -- they are examples.

## Calamares Settings

At a high level, the [`settings.conf`][settings.conf]
file defines a *sequence* of things
to do (actions) during an installation. This defines the order in which
user-visible actions are taken (e.g. configuring the timezone) as well as
internal actions (e.g. installing the bootloader).

The sequence itself is split into a *show* and *exec* sections:
the *show* parts are user-visible, and then *exec* does the work.
During each *exec* section, the branding slideshow is displayed,
and before each *exec* section, the user may be prompted to
allow to continue the process. Normal installations have a
*show* section for user interaction, and then a single *exec* section,
and then a *show* section with only the "finished" module.

A viewmodule like "users" has a user-visible part for collecting
information, and **may** also have a non-interactive part to do the actual
work. Some viewmodules are therefore listed twice: once in a *show*
section and once in an *exec* section.

A viewmodule in an *exec* section will not show a UI.

A section names module instances. Most modules are simply
listed as `<modulename>`, which means the same as `<modulename>@<modulename>`
and uses the configuration file `<modulename>.conf`. It is possible
to define specific module instances (for instance, to run the same
module multiple times with different configuration files).
See [`settings.conf`][settings.conf] for details.

For internal actions, there are different kinds of Calamares modules
which differ in how they are configured. These can be used to run
commands or perform changes to the target system during installation:
 - Always executing one command (*process* modules). Using this
   means creating a new module directory under `$USC/modules` with a
   suitable `module.desc` file that defines the command to run. This
   is no longer recommended.
 - Always executing one or more commands (*shellprocess* instances).
   Using this means defining an instance in `settings.conf` and
   adding a suitable configuration file with the list of commands
   to the configuration directory, e.g. to `$USC/modules/shellprocess`.
   This has the (slight) advantage that it does require intermediate
   directories, and has better error handling.
 - Conditionally executing one or more commands (*contextualprocess*
   instances). This needs an instance and a configuration file
   describing which conditions (expressed as values in the Calamares
   global configuration) cause which commands to run.
 - Conditionally executing one or more commands (*python* job)
   with arbitrary logic. This means creating a new module directory
   under `$USC/modules` and writing a `main.py`. This allows much
   more expressive logic than *contextualprocess* instances, at
   the expense of more complicated packaging and programming.
   Most existing Calamares modules are of this type.
 - Conditionally executing one or more commands (*C++* job)
   with arbitrary logic. This means creating a new module directory
   in the Calamares source tree and adding suitable C++ and CMake
   code to get it to compile. This is the most programmer-intensive
   form of modules, but does allow the most access to Calamares
   internals.

## Modules Settings

Calamares is usually shipped with *PREFIX* set to `/usr`,
so that most of its configuration is in `/usr/share/calamares`.
In the table below, `$USC` means that directory.

| File name | Deployment path (in search priority) | Purpose |
|:---------:|:------------------------------------:|:-------:|
| [`branding.desc`][branding.desc] | `$USC/`_`brand_name`_ | Branding descriptor file, shipped with the rest of your branding component and selected in `settings.conf` |
| _`modulename`_`.conf`, e.g. `partition.conf`, `grubcfg.conf`, `unpackfs.conf`, ... | `/etc/calamares/modules`, `$USC/modules` | Configuration files for every module that needs one |
| [`displaymanager.conf`][displaymanager.conf] | | Configuration for the display manager, select SDDM, lightdm, ... |
| [`locale.conf`][locale.conf] | | Locale and TimeZone configuration. GeoIP settings. |
| [`partition.conf`][partition.conf] | | Configuration for (automatic) partitioning and swap |

[modules]: https://github.com/calamares/calamares/blob/master/src/modules
[settings.conf]: https://github.com/calamares/calamares/blob/master/settings.conf
[branding.desc]: https://github.com/calamares/calamares/blob/master/src/branding/default/branding.desc
[displaymanager.conf]: https://github.com/calamares/calamares/blob/master/src/modules/displaymanager/displaymanager.conf
[locale.conf]: https://github.com/calamares/calamares/blob/master/src/modules/locale/locale.conf
[partition.conf]: https://github.com/calamares/calamares/blob/master/src/modules/partition/partition.conf



## Specialized Settings

> This section handles specialized settings or weird
> ways to start Calamares with specific configurations.

### How to launch Calamares with custom settings?
For most distributions, launching Calamares from the provided .desktop file is sufficient.  When that is not the case, for example, you want to launch from a custom application or want to use a custom command line instead of the default ```Exec=pkexec /usr/bin/calamares``` you can create a script to use instead.

An example of such a script:

````
#!/bin/sh

if [ ! -f /var/log/nvidia ] && [ ! -f /var/log/nvidia-340xx ]; then
    sudo sed -i -e 's|- license|#- license|' /usr/share/calamares/settings.conf
fi

sudo /usr/bin/calamares -d > installation.log
```
This example shows the option to not show the License page if free graphics drivers are in use.  It also shows creating an installation.log in the user's home directory.
Such a script can be used in the default calamares.desktop, simply use a sed line to replace ```Exec=pkexec /usr/bin/calamares``` with ```Exec=/usr/bin/launch-calamares.sh```.

### How to integrate Calamares with the used system style and theme?
#### Integration when launched with sudo
When launching Calamares from a custom script using sudo, it does not inherit any of the system styles.  A way to get this to integrate again is by setting some env variables in your `/etc/sudoers` file used in the Live system.
Example of what to add to the end of that file:
```
Defaults env_keep += "QTDIR PATH QT_PLUGIN_PATH QT_INCLUDE_PATH QML2_IMPORT_PATH KDE_SESSION_VERSION KDE_FULL_SESSION"
```
