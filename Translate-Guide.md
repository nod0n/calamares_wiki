# Translator's Guide

Calamares uses [Transifex](https://www.transifex.com/) as its translation
inrfastructure.
The [project overview](https://www.transifex.com/calamares/calamares/) for Calamares
shows which languages exist and how translated they are.
Translations are (semi-)regularly updated from the *calamares* (development)
branch of Calamares and sent to Transifex; updated translations are
imported into the same *calamares* branch.

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
view strings in the *calamares* resource,
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

## Timezone Translations

The global list of timezones is made of names like *Europe/Amsterdam*,
*Africa/Harare* and *Asia/Tokyo* -- those are regions and cities.
The timezone page (generally part of the *locale* module) displays
these names. It is possible to translate those names for local
use (e.g. the zone "Berlin" is called "Berlijn" in Dutch, because
that is the name of the city in Dutch).

The regions and city names are **not** in Transifex, because it would
add about 600 names to the translation load, and it's not entirely
clear how many languages would even use the option of translating
or transliterating region and zone names.

To translate region and timezone names:

- Take [this source file](https://github.com/calamares/calamares/blob/calamares/lang/tz_en.ts)
  with the region and zone names.
- Enter the translations by hand in that file, putting the translation in
  the `<translation>` element and removing the `type` attribute; for instance,
  if transliterating [Dushanbe](https://en.wikipedia.org/wiki/Dushanbe)
  into Cyrillic:
  ```
    <message>
        <location filename="../src/libcalamares/locale/ZoneData_p.cxxtr" line="176"/>
        <source>Dushanbe</source>
        <comment>tz_names</comment>
        <translation>Душанбе</translation>
    </message>
  ```
- Add that file with translations as `lang/tz_<lang>.ts` where
  *lang* is the language code, and submit the changed file to
  the Calamares source repository.



## Special Cases

 - Esperanto was not available as a language in Qt prior to Qt 5.12.2.
   To use Esperanto you need a sufficiently new Qt version.
 - Interlingue exists as a translation, but no version of Qt supports it.
 - Serbian is available both in Cyrillic and Latin spellings,
   but the language name for the Latin spelling is non-standard
   in Calamares (for historical reasons), *sr@latn*.
