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

You'll need a [Transifex account](https://www.transifex.com/signup/).
Follow their instructions, they
are well-written. Then apply to join the Calamares team.
Give us a shout on IRC or in a
[Calamares issue](https://github.com/calamares/calamares/issues), so that we
know you want to join.

A message on Transifex via their usual 
[request-new-language workflow](https://www.transifex.com/calamares/calamares/languages/) will also work.

## Translation Guidelines

Just do it. Please don't change keyboard shortcuts unless you
really really have to (keyboard shortcuts can be recognized by having
*&letter* in the string).

## New Languages

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

## Testing New Languages

A new language is added to the *bad* list. That means that the translation
is at 0% completion, not that the translation itself is bad. As the translation
is completed, the language will be moved from one completion-status to another
within the Calamares code.

If you have a `.ts` file, you can test a new translation inside Calamares,
but you must be able to compile Calamares (see the
[developer's guide](Develop-Guide.md)
) and you must **add** the language to the list that Calamares
will support.

 - Place the new translation file in the directory `lang/`. Usually this
   means you will add a file called `calamares_`*language*`.ts`.
 - Open `CMakeLists.txt` in a text editor.
 - Scroll down to the section labeled *Transifex (languages) info*,
   or search for `tx_good`.
 - Add your language code to the list, e.g.
   ```
set( _tx_good MyNewLanguage es sq he hu )
```
 - Rebuild Calamares.
