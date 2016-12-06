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