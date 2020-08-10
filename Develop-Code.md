# Hacking on Calamares

These are the guidelines for hacking on Calamares. Except for the licensing,
which **must** be GPLv3+, these are guidelines and -- like PEP8 -- the most
important thing is to know when you can ignore them.


## Licensing

Calamares is released under the terms of the GNU GPL, version 3 or later.
Every source file must have a license header, with a list of copyright
holders and years.

Calamares uses [SPDX](https://spdx.org/) identifiers in headers
(when they are updated in 2020) and tries to follow the
[Reuse Software](https://reuse.software/) best-practices
for license annotation. Each file should start with a list of
*SPDX-FileCopyrightText* lines, followed by one single
*SPDX-License-Identifier*. Almost all of Calamares uses
*GPL-3.0-or-later*.

Don't include a *LICENSE* line -- that was for an earlier revision
of the spec.

Example:
```
/* === This file is part of Calamares - <https://github.com/calamares> ===
 *
 *   SPDX-FileCopyrightText: 2013-2014 Random Person <name@example.com>
 *   SPDX-FileCopyrightText: 2010 Someone Else <someone@example.com>
 *   SPDX-License-Identifier: GPL-3.0-or-later
 */
```

It is **optional** (but not recommended ) to include some more
license text; most of the older Calamares files do that:
```
/*
 *   Calamares is free software: you can redistribute it and/or modify
 *   it under the terms of the GNU General Public License as published by
 *   the Free Software Foundation, either version 3 of the License, or
 *   (at your option) any later version.
 *
 *   Calamares is distributed in the hope that it will be useful,
 *   but WITHOUT ANY WARRANTY; without even the implied warranty of
 *   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
 *   GNU General Public License for more details.
 *
 *   You should have received a copy of the GNU General Public License
 *   along with Calamares. If not, see <http://www.gnu.org/licenses/>.
 */
```
Copyright holders must be physical or legal personalities. A statement such as
`Copyright 2014, The FooBarQuux project` has no legal value if "The FooBarQuux
project" is not the legal name of a person, company, incorporated
organization, etc.

Please add your name to files you touch when making any contribution (even if
it's just a typo-fix which might not be copyrightable in all jurisdictions).

- *Do* add your name when fixing things.
- *Don't* update years randomly; just use the current year when you
  add yourself to a file for the first time.
- *Do* make sure the license-identifier and license-filename are correct;
  not everything is GPLv3.

### Exceptions in Licensing

There is third-party code in Calamares. The licenses for that
third-party code are included, and are GPLv2-or-later (for use in Calamares, that means
GPLv3-or-later), LGPLv2 and LGPLv2.1 and LGPLv3, MIT (2-clause) and BSD (3-clause).
See the `LICENSES/` directory for the texts, and `3rdparty/` for most of the relevant code.

Some **scripts** related to Calamares are released under a BSD (2-clause) license.
They are not part of the Calamares executable, but are used in the production
process (e.g. the translation-support scripts). These are liberally licensed since
they can easily be adopted by other projects.


## C++ Style

> When in doubt, look at existing code for style guidance and
> match that. Then apply the formatting tools.

This formatting guide applies to C++ code only.

* Spaces, not tabs.
* Indentation is 4 spaces.
* Lines should be limited to 90 characters.
* Spaces between brackets and argument functions, including for template arguments.
* No space before brackets, except for keywords, for example `function( argument )` but
  `if ( condition )`.
* For pointer and reference variable declarations, put a space before the variable name
  and no space between the type and the `*` or `&`, e.g. `int* p`.
* `for`, `if`, `else`, `while` and similar statements put the braces on
  the next line. Always use braces.
* Function and class definitions have their braces on separate lines.
* A function implementation's return type is on its own line.
* `CamelCase.{cpp,h}` style file names.
* Lambdas are preferrably indented to a 4-space tab, even when passed as an
  argument to functions.
* Use C++14 features; avoid `foreach`, for instance.

Example:
```
bool
MyClass::myMethod( QStringList list, const QString& name )
{
    if ( list.isEmpty() )
    {
        return false;
    }
    cDebug() << "Items in list ..";
    for ( const QString& string : list )
    {
        cDebug() << "  .." << string;
    }

    switch ( m_enumValue )
    {
    case Something:
        return true;
    case SomethingElse:
        doSomething();
        break;
    }
}
```

You can use `clang-format` (version 7 or later) to have Calamares sources formatted
the right way. There is a `.clang-format` file that specifies the details.

**NOTE:** There is a shell script `ci/calamaresstyle` which uses a combination
of astyle and clang-format to apply the full style guide. It should be run at least
once on a code branch that is about to be merged-in, on files modified in that branch.
Commit style-changes separately from functional changes (and prefer doing the functional
changes in the right style to begin with).

**NOTE:** An .editorconfig file is included to assist with formatting. In
order to take advantage of this functionality you will need to acquire the
[EditorConfig](http://editorconfig.org/#download) plug-in for your editor.


### Naming

* Use CamelCase for everything.
* Local variables should start out with a lowercase letter.
* Class names are capitalized
* Prefix class member variables with `m_`, e.g. `m_queue`.
* Prefix static member variables with `s_`, e.g. `s_instance`.
* Functions are named in the Qt style, like Java's, without the 'get' prefix.
    * A getter is `variable()`.
    * If it's a getter for a boolean, prefix with 'is', so `isCondition()`.
    * A setter is `setVariable( arg )`.


### Includes

Header includes should be listed in the following order:

* own header,
* Calamares includes,
* includes for Qt-based libraries,
* Qt includes,
* STL includes,
* other includes.

They should also be sorted alphabetically for ease of locating them.

Includes in a header file should be kept to the absolute minimum, as to keep
compile times short. This can be achieved by using forward declarations
instead of includes, like `class QListView;`.

Example:
```
#include "Settings.h"

#include "CalamaresApplication.h"
#include "utils/CalamaresUtils.h"
#include "utils/Logger.h"

#include <QDir>
#include <QFile>
```

Use include guards, not `#pragma once`. Use "namespaced" include guards.


### C++ tips

All C++11 and C++14 features are acceptable, and the use of new C++14 features is encouraged when
it makes the code easier to understand and more maintainable.

The use of `nullptr` is preferred over the use of `0` or `NULL`.

For all containers, the
range-based `for` syntax introduced with C++11 is preferred.

When re-implementing a virtual method, always add the `override` keyword.

Try to keep your code const correct. Declare methods const if they don't mutate the
object, and use const variables. It improves safety, and also makes it easier to
understand the code.

For the Qt signal-slot system, the new (Qt5) syntax is to be preferred because it allows
the compiler to check for the existence of signals and slots. As an added benefit, the
new syntax can also be used with `tr1::bind` and C++11 lambdas. For more information, see
the [Qt wiki][2].

Example:
```
connect( m_next, &QPushButton::clicked, this, &ViewManager::next );

connect( m_moduleManager, &Calamares::ModuleManager::modulesLoaded, [this]
{
    m_mainwindow->show();
});
```

[2]: https://wiki.qt.io/New_Signal_Slot_Syntax

## Python Style

For Python modules, we use
[pycodestyle](https://github.com/PyCQA/pycodestyle) to apply a check of
some PEP8 guidelines.

## Debugging

> For Calamares, "debugging" mostly means logging, since problems will
> usually be on someone else's machine while they are installing.

### C++ Logging

Use `cDebug()` from `utils/Logger.h`. You can pass a debug-level to the
macro (6 is debugging, higher is less important). Use `cWarning()` for warning
messages (equivalent to level 2) and `cError()` for errors (level 1). Warnings
and errors will add relevant text automatically. See `libcalamares/utils/Logger.h`
for details.

For log messages that are continued across multiple calls to `cDebug()`,
in particular listing things, conventional formatting is as follows:
* End the first debug message with ` ..`
* Start the next debug message by outputting `Logger::SubEntry`

For single-outputs that need to be split across multiple lines,
output `Logger::Continuation`.

There are a bunch of convenience items in the `Logger` namespace,
see the header for more details.


### Python Logging

In Python modules, `libcalamares.utils` provides methods `debug()` and `warning()`
which take a single string to output.


## Commit Messages

Keep commit messages short(-ish) and try to describe what is being changed
*as well as why*. Use the commit keywords for GitHub, especially *FIXES:*
to auto-close issues when they are resolved.

For functional changes to Calamares modules or libraries, try to put
*[modulename]* in front of the first line of the commit message.

For non-functional changes to infrastructure, try to label the change
with the kind of change, e.g. *CMake* or *i18n* or *Documentation*.
