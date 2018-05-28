# User's Guide

> This section of the wiki is about **using** Calamares to install
> your system. If you are reading this, it is probably because
> your distribution doesn't have more detailed specific instructions.

## Installation Tracking

Calamares can be configured to do installation tracking. If it is,
you will get a page in the installation process that asks you to
enable installation tracking. If, and only if, you enable installation
tracking, then Calamares will send information **once only** about
your hardware to the server configured by the distribution.

This is used by distributions to count how often they are installed,
and on what kinds of machines -- which helps the distribution tailor
its packages to the machines its users typically use.

When Calamares sends information about the hardware, it sends:
- the make and model of your CPU
- the amount of main memory
- the total amount of attached disk
As part of sending this information, Calamares necessarily makes
your IP address known to the receiving party.

