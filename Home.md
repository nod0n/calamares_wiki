# Calamares

[![GitHub release](https://img.shields.io/github/release/calamares/calamares.svg)](https://github.com/calamares/calamares/releases)
[![Travis Build Status](https://travis-ci.org/calamares/calamares.svg?branch=master)](https://travis-ci.org/calamares/calamares)
[![Coverity Scan Build Status](https://scan.coverity.com/projects/5389/badge.svg)](https://scan.coverity.com/projects/5389)
[![GitHub license](https://img.shields.io/github/license/calamares/calamares.svg)](https://github.com/calamares/calamares/blob/master/LICENSE)

> [Calamares](https://calamares.io/) is the universal installer framework.
> This wiki is for *contributors* to Calamares: the
> *translators*, *developers*, and *deployers*
> using Calamares as a framework / application for installing a Linux system.
> There is also generic *user* documentation that applies to installing-your-
> system, which might be used by a distro as a basis for their own installer
> documentation.

For Calamares, *universal* means that Calamares can be configured
for any Linux distribution to act as installer for that distribution's
ISO and memory-stick images. It does **not** mean that
Calamares, as provided in source, can install any Linux.
*Users* of Calamares are the people who make the distribution
images, not the people who eventually install those images
(although there's a [user's guide](Use-Guide) too).

## Support

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
[building](Develop-Guide#build) Calamares,
on its
[design](Develop-Design),
and [localization](Develop-Guide#i18n).
Much of the technical documentation is in README files in the
source code, though, where it is much closer to the things it
documents. Of particular interest is the `/ci/` directory, which is where things-that-are-not-Calamares-itself but related to Calamares development (like the Continuous Integration scripts, like translation tools, and development documentation) end up.

* [Coding Style](Develop-Code)
* [Release Process :clipboard:](https://github.com/calamares/calamares/blob/master/ci/RELEASE.md)

## Tester's Guide

While developing Calamares, it needs [testing](Test-Guide) on virtual or physical machines. The Tester's Guide provides advice on setting up test-systems. It also explains the acceptance tests that should (or might) be done on Calamares.

## Deployer's Guide

The [deployer's guide](Deploy-Guide) describes how to deploy
Calamares in your distribuion Live CD or other medium. This is the
guide to read when Calamares works and runs properly on your development
systems, and you're ready to integrate it with the distribution's install medium.

(In addition there is a list of [known issues](Deploy-Issues), common
[configuration tips](Deploy-Configuration)  and some
documentation on specialized topics like [LUKS](Deploy-LUKS))

## User's Guide

The [user's guide](Use-Guide) is intended to be generic instructions for
installing with Calamares, describing the various end-user visible parts
of the installation process. This is intended as a *starting point* for
distribution documentation, not as a replacement for it.


## Translator's Guide

You can help by [translating Calamares into your own language](https://www.transifex.com/calamares/calamares/)!

There are some general translation guidelines in the
[translator's guide](Translate-Guide).

## Code of Conduct

(*draft 2018-05-28*)

The Calamares community -- of developers, translators, and downstream (distro) users --
aims to be courteous, professional, and inclusive. Harrassment, discriminatory
statements and abuse are not tolerated. In general, we apply the
[KDE Code of Conduct](https://www.kde.org/code-of-conduct/) and the
[GNOME Code of Conduct](https://wiki.gnome.org/Foundation/CodeOfConduct) (the
rules of decent behavior in both communities are pretty much the same).

This has a few consequences where the admins of the Calamares community
will take action:
 - abusive and harrassing comments on issues and pull-requests will be removed.
 - off-topic and irrelevant comments on issues and pull-requests may be removed
   (off-topic comments that could be a topic in their own right, may be
   re-posted to a relevant issue though).

Other considerations in comments:
 - comment on code, comment on design, ask questions about the intent of
   feature requests or pull-requests, but assume good intentions. A request
   is just a request, and "no" is just as good as "no, that's stupid".
 - English is the common language of the project. It is the first language
   of a minority of the project. Take language issues into account.
 - Non-English comments will probably be ignored or removed; special
   circumstances may apply.
