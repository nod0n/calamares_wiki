# Translator's Guide

Calamares uses [Transifex](https://www.transifex.com/) as its translation
inrfastructure. Internally, the program uses Qt translations and GNU
gettext. Translations are (semi-)regularly updated from the *master*
branch of Calamares and sent to Transifex; updated translations are
imported into the same *master* branch.

This means that stable releases don't get translation updates --
I have not thought of a good way to do that with one Calamares
project in Transifex.

## Getting Started

You'll need a Transifex account. Follow their instructions, they
are well-written. Then apply to join the Calamares team.
Give us a shout on IRC or in a
[Calamares issue](https://github.com/calamares/calamares/issues), so that we
know you want to join.

## Translation Guidelines

Just do it. Please don't change keyboard shortcuts unless you
really really have to (keyboard shortcuts can be recognized by having
*&letter* in the string).

## Getting Started

A new translation is added to Calamares when the team is created,
but the language is not enabled for **use** in Calamares until
some strings have been translated. You can mention on IRC or via
a Transifex message to have the language enabled (and for most
releases, the language lists are updated with the current status
from Transifex).

If you use Transifex's filters to pick strings, you can use the following
*occurrence* values (view strings in the *calamares-master* repository,
then click *more* in the tab-bar of filters along the top of the list,
and then click *occurrence* and enter a filter-string)
to pick strings that show up early in the application,
so that it is easy to see if the language works:

 -   *Welcome*
 -   *Debug*
 -   *Finished*

These modules are generally enabled in every Calamares setup, so
they are highly visible at the beginning and end of using Calamares.
