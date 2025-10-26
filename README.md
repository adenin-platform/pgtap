pgTAP 1.3.5
===========

[pgTAP](https://pgtap.org) is a unit testing framework for PostgreSQL written
in PL/pgSQL and PL/SQL. It includes a comprehensive collection of
[TAP](https://testanything.org)-emitting assertion functions, as well as the
ability to integrate with other TAP-emitting test frameworks. It can also be
used in the xUnit testing style. For detailed documentation, see the
documentation in `doc/pgtap.mmd` or
[online](https://pgtap.org/documentation.html "Complete pgTAP Documentation").

[![PGXN version](https://badge.fury.io/pg/pgtap.svg)](https://badge.fury.io/pg/pgtap)
[![🐘 Postgres](https://github.com/theory/pgtap/actions/workflows/test.yml/badge.svg)](https://github.com/theory/pgtap/actions/workflows/test.yml)

pgTAP must be installed on a host with PostgreSQL server running; it cannot
be installed remotely.

To build it, just do this:

```sh
make
make install
make installcheck
```

If you encounter an error such as:

```
"Makefile", line 8: Need an operator
```

You need to use GNU make, which may well be installed on your system as
`gmake`:

```sh
gmake
gmake install
gmake installcheck
```

If you encounter an error such as:

```
make: pg_config: Command not found
```
Or:

```
Makefile:52: *** pgTAP requires PostgreSQL 9.1 or later. This is .  Stop.
```

Be sure that you have `pg_config` installed and in your path. If you used a
package management system such as RPM to install PostgreSQL, be sure that the
`-devel` package is also installed. If necessary tell the build process where
to find it:

```sh
env PG_CONFIG=/path/to/pg_config make && make install && make installcheck
```

And finally, if all that fails, copy the entire distribution directory to the
`contrib/` subdirectory of the PostgreSQL source tree and try it there without
`pg_config`:

```sh
env NO_PGXS=1 make && make install && make installcheck
```

If you encounter an error such as:

```
ERROR:  must be owner of database regression
```

You need to run the test suite using a super user, such as the default
"postgres" super user:

```
make installcheck PGUSER=postgres
```

If you encounter an error such as:

```
ERROR: Missing extensions required for testing: citext isn ltree
```

Install the PostgreSQL
[Additional Supplied Modules](https://www.postgresql.org/docs/current/contrib.html),
which are required to run the tests. If you used a package management system
such as RPM to install PostgreSQL, install the `-contrib` package.

To install the extension in a custom prefix on PostgreSQL 18 or later, pass
the `prefix` argument to `install` (but no other `make` targets):

```sh
make install prefix=/usr/local/extras
```

Then ensure that the prefix is included in the following [`postgresql.conf`
parameters]:

```ini
extension_control_path = '/usr/local/extras/postgresql/share:$system'
dynamic_library_path   = '/usr/local/extras/postgresql/lib:$libdir'
```

Once pgTAP is installed, you can add it to a database by connecting as a super
user and running:

```sql
CREATE EXTENSION pgtap;
```

If you've upgraded your cluster to PostgreSQL 9.1 and already had pgTAP
installed, you can upgrade it to a properly packaged extension with:

```sql
CREATE EXTENSION pgtap FROM unpackaged;
```

If you want to install pgTAP and all of its supporting objects into a specific
schema, use the `SCHEMA` clause to specify the schema, like so:

```sql
CREATE EXTENSION pgtap SCHEMA tap;
```

Development Environment
-----------------------

For development and testing, you can use the provided devcontainer configuration which includes PostgreSQL 17 and all necessary dependencies:

1. Open the repository in VS Code
2. When prompted, click "Reopen in Container" or run the "Dev Containers: Reopen in Container" command
3. The devcontainer will automatically:
   - Install PostgreSQL 17 with contrib extensions
   - Install all build dependencies (make, perl, patch, etc.)
   - Start the PostgreSQL service
   - Set up a development database

Once the container is running, you can build pgTAP with:

```sh
make
```

This will generate the required SQL files:
- `sql/pgtap.sql`
- `sql/uninstall_pgtap.sql`
- `sql/pgtap-core.sql`
- `sql/pgtap-schema.sql`

Dependencies
------------

pgTAP requires PostgreSQL 9.1 or higher.

Copyright and License
---------------------

Copyright (c) 2008-2025 David E. Wheeler. Some rights reserved.

Permission to use, copy, modify, and distribute this software and its
documentation for any purpose, without fee, and without a written agreement is
hereby granted, provided that the above copyright notice and this paragraph
and the following two paragraphs appear in all copies.

IN NO EVENT SHALL DAVID E. WHEELER BE LIABLE TO ANY PARTY FOR DIRECT,
INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES, INCLUDING LOST
PROFITS, ARISING OUT OF THE USE OF THIS SOFTWARE AND ITS DOCUMENTATION, EVEN
IF DAVID E. WHEELER HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

DAVID E. WHEELER SPECIFICALLY DISCLAIMS ANY WARRANTIES, INCLUDING, BUT NOT
LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A
PARTICULAR PURPOSE. THE SOFTWARE PROVIDED HEREUNDER IS ON AN "AS IS" BASIS,
AND DAVID E. WHEELER HAS NO OBLIGATIONS TO PROVIDE MAINTENANCE, SUPPORT,
UPDATES, ENHANCEMENTS, OR MODIFICATIONS.
