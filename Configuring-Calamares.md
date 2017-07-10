Calamares is designed to be thoroughly configurable and suitable for a wide range of use cases.

End users are not supposed to deal with configuration issues, so all configuration is done through configuration files. The files named here link to the sample configuration files shipped with Calamares, which also contain the documentation for the settings in each file.

| File name | Deployment path (in search priority) | Purpose |
|:---------:|:------------------------------------:|:-------:|
| [`settings.conf`][settings.conf] | `/etc/calamares`, `/usr/share/calamares` | Main Calamares configuration file |
| _`modulename`_`.conf`, e.g. `partition.conf`, `grubcfg.conf`, `unpackfs.conf`, ... | `/etc/calamares/modules`, `/usr/share/calamares/modules` | Configuration files for every module that needs one |
| [`partition.conf`][partition.conf] | | Configuration for (automatic) partitioning and swap |
| [`displaymanager.conf`][displaymanager.conf] | | Configuration for the display manager, select SDDM, lightdm, ... |
| [`branding.desc`][branding.desc] | `/usr/share/calamares/`_`branding_component_name`_ | Branding descriptor file, shipped with the rest of your branding component and selected in `settings.conf` |

[settings.conf]: https://github.com/calamares/calamares/blob/master/settings.conf
[branding.desc]: https://github.com/calamares/calamares/blob/master/src/branding/default/branding.desc
[displaymanager.conf]: https://github.com/calamares/calamares/blob/master/src/modules/displaymanager/displaymanager.conf
[partition.conf]: https://github.com/calamares/calamares/blob/master/src/modules/partition/partition.conf