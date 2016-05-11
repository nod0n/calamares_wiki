Calamares is designed to be thoroughly configurable and suitable for a wide range of use cases.

End users are not supposed to deal with configuration issues, so all configuration is done through configuration files.

| File name | Deployment path (in search priority) | Purpose |
|:---------:|:------------------------------------:|:-------:|
| `settings.conf` | `/etc/calamares`, `/usr/share/calamares` | Main Calamares configuration file |
| _`modulename`_`.conf`, e.g. `partition.conf`, `grubcfg.conf`, `unpackfs.conf`, ... | `/etc/calamares/modules`, `/usr/share/calamares/modules` | Configuration files for every module that needs one |
| `branding.desc` | `/usr/share/calamares/`_`branding_component_name`_ | Branding descriptor file, shipped with the rest of your branding component and selected in `settings.conf` |