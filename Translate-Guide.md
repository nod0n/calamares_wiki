# Translator's Guide

Calamares uses [Transifex](https://www.transifex.com/) as its translation
inrfastructure.
The [project overview](https://www.transifex.com/calamares/calamares/) for Calamares
shows which languages exist and how translated they are.
Translations are (semi-)regularly updated from the *master*
branch of Calamares and sent to Transifex; updated translations are
imported into the same *master* branch.

This means that stable releases don't get translation updates --
I have not thought of a good way to do that with one Calamares
project in Transifex.

Internally, the program uses **both** Qt translations and GNU
gettext. This is invisible for the translator, but it does mean
that the same string can show up in two different Calamares string collections.

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

 - Just do it. You do not need permission to write translations for Calamares,
   and we are happy with each new language.
 - Please don't change keyboard shortcuts unless you
   really really have to (keyboard shortcuts can be recognized by having
   *&letter* in the string).
   For Indic languages (Assamese, Hindi and Malayalam and others)
   the keyboard shortcuts are generally left in Latin-1.

## New Languages

See [getting started](#GettingStarted) for information on how to begin.
Just poke the Calamares team -- via IRC or Transifex to GitHub issue --
to get the team started.

A new translation is added to the Calamares source when the team is created,
but the language is not enabled for **use** in Calamares until
some strings have been translated. There are automatic thresholds
for use: you must reach 5% translation (about 30 strings)
before the language will be enabled in a Calamares release.
The list of languages is updated with each Calamares release,
or give a shout on IRC when things have been updated.

The **first strings** to translate are the `.desktop` file and a few
common Calamares modules.
Use Transifex's filters to pick strings from those modules:
view strings in the *calamares-master* repository,
then click *more* in the tab-bar of filters along the top of the list,
and then click *occurrence* and enter a filter-string from the list below.
These strings show up early in the application,
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

With Calamares release 3.2.19 (february 2020) it is much easier
to test translations without bulding Calamares. See below.

### Before Calamares 3.2.19

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
 - Run Calamares and pick the language from the drop-down, or set the *LANG*
   environment variable to test if Calamares starts up natively.

### Since Calamares 3.2.19

If you have a `.ts` file, you can test an **existing** translation
(updates, modifications) with Calamares. You will need the
Qt translation tools installed -- in particular, you need to be able
to run `lrelease` (or `lrelease-qt5`) to build a `.qm` file.

In general:
 - Place your `.ts` file somewhere. Call it `calamares_<lang>.ts`,
   for instance `calamares_en.ts` for an English-language translation.
 - Compile it to `.qs` with using `lrelease`.

The compilation process looks like this:
```
$ lrelease calamares_en.ts 
Updating 'calamares_en.qm'...
    Generated 609 translation(s) (607 finished and 2 unfinished)
    Ignored 2 untranslated source text(s)
```

You can deploy the file in three different ways:
 - (All Calamares) Copy the `.qs` file to the `lang/` subdirectory
   of the Calamares data directory. This usually means
   copy it to `/usr/share/calamares/lang/`.
   This is intended for distributions, since it needs no changes
   to the way Calamares is normally run.
 - (All Calamares) Run Calamares with the `-T`
   command-line argument. It will read the translation from the current
   directory.
   This is intended for translators who are working on a particular
   translation.
 - (Locally-built Calamares) Run Calamares with the `-d` command-line
   argument. This runs *debug mode*, which loads all configuration and
   settings and modules from the current directory. This is intended for
   developers.

## Special Cases

 - Esperanto was not available as a language in Qt prior to Qt 5.12.2.
   To use Esperanto you need a sufficiently new Qt version.
 - Serbian is available both in Cyrillic and Latin spellings,
   but the language name for the Latin spelling is non-standard
   in Calamares (for historical reasons), *sr@latn*.
