# Calamares

> [Calamares](https://calamares.io/) is the universal installer framework.
> This wiki is for *developers* working on Calamares, and for *deployers*
> using Calamares as a framework / application for installing a Linux system.
> There is also generic *user* documentation that applies to installing-your-
> system, which might be used by a distro as a basis for their own installer
> documentation.

## Support

* [FAQ](FAQ)
* [Bug Reports](https://github.com/calamares/calamares/issues)
* [Continuous Integration](https://travis-ci.org/calamares/calamares)
* [IRC](irc://irc.freenode.net/calamares) - #calamares on Freenode
  ([webchat](https://webchat.freenode.net/?randomnick=1&channels=%23calamares))

## How to Help

* Send good [Bug Reports](https://github.com/calamares/calamares/issues)
* Contribute [Translations](https://www.transifex.com/calamares/calamares/)
* Contribute [Code](https://github.com/calamares/calamares/)
* Contribute [Documentation](https://github.com/calamares/calamares/wiki/)

## Developer's Guide

The developer's guide contains information on
[building](Develop-Guide.md#build) Calamares,
on its
[design](Develop-Guide.md#design),
and [localization](Develop-Guide.md#i18n).
Much of the technical documentation is in README files in the
source code, though, where it is much closer to the things it
documents. Of particular interest is the `/ci/` directory, which is where things-that-are-not-Calamares-itself but related to Calamares development (like the Continuous Integration scripts, like translation tools, and development documentation) end up.

* [Coding Style](https://github.com/calamares/calamares/blob/master/ci/HACKING.md)
* [Release Process](https://github.com/calamares/calamares/blob/master/ci/RELEASE.md)

## Tester's Guide

While developing Calamares, it needs [testing](Test-Guide) on virtual or physical machines. The Tester's Guide provides advice on setting up test-systems. It also explains the acceptance tests that should (or might) be done on Calamares.

## Deployer's Guide

The [deployer's guide](Deploy-Guide) describes how to deploy
Calamares in your distribuion Live CD or other medium. This is the
guide to read when Calamares works and runs properly on your development
systems, and you're ready to integrate it with the distribution's install medium.

(In addition there is a list of [known issues](Deploy-Issues), common
[configuration tips](Deploy-xConfiguration)  and some
documentation on specialized topics like [LUKS](Deploy-xLUKS))

## User's Guide

The [user's guide](Use-Guide) is intended to be generic instructions for
installing with Calamares, describing the various end-user visible parts
of the installation process. This is intended as a *starting point* for
distribution documentation, not as a replacement for it.


## Translator's Guide

You can help by [translating Calamares into your own language](https://www.transifex.com/calamares/calamares/)!

There are some general translation guidelines in the
[translator's guide](Translate-Guide).

