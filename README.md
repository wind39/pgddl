DDL extractor functions  for PostgreSQL
=======================================

This is an SQL-only extension for PostgreSQL that provides uniform functions for generating 
SQL DDL scripts for objects stored in a database. It contains a lot of foo to convert
Postgres system catalogs to nicely formatted SQL snippets.

Some other SQL databases support commands like SHOW CREATE TABLE or provide 
other fascilities for the purpose. 

PostgreSQL currently doesn't provide overall in-server DDL extracting functions,
but rather just a separate `pg_dump` program. It is an external tool to the server 
and therefore requires shell access or local installation to be of use.

PostgreSQL however already provides a number of helper functions which greatly help with
reconstructing DDL and are of course used by this extension.
It also has sophisticated query capabilities which make this project possible.

Advantages over using other tools like `psql` or `pgdump` include:

- You can use it extract DDL with any client which support running plain SQL queries
- With SQL you can select things to dump by using usual SQL semantics (WHERE, etc)
- Created scripts are somewhat more intended to be run and copy/pasted manually by the DBA
  into other databases/scripts. This means prefering ALTER to CREATE, creating indexes which
  are part of a constraint with ADD CONSTRAINT and such.
- No shell access or shell commands with hairy options required (for running pg_dump), just use SELECT!

Some disadvantages:

- Not all Postgres objects are supported. It provides support for the basic user-level objects. 
- It is not well tested at all. While it contains a number of regression tests, these can be
  hardly considered as proofs of correctness. Be certain there are bugs. Use at your own risk!
- It is kind of slow-ish for complicated stuff stuff

It is currently rather incomplete, but still useful. 

Tested on PostgreSQL 9.6. Might work with earlier versions.

Plans on how to make this support newer fetures AND older servers are being considered.
 

Installation
------------

To build and install this module:

    make
    make install
    make install installcheck

or selecting a specific PostgreSQL installation:

    make PG_CONFIG=/some/where/bin/pg_config
    make PG_CONFIG=/some/where/bin/pg_config install

And finally inside the database:

    CREATE EXTENSION ddl;

It you use multiple schemas, you will need to have variable `search_path` 
set appropriately for the extension to work. To make it work with any value of
`search_path`, you can install the extension in the `pg_catalog` schema:

    CREATE EXTENSION ddl SCHEMA pg_catalog;

This of course requires superuser privileges.

Using
-----

This module provides one main end user function `pg_ddl_script` that 
you can use to obtain SQL DDL source for a particular database object.

Currently supported object types are `regclass`,`regtype`,`regproc`,`regprocedure` 
and `regrole`. You will probably want to cast object name or oid to the appropriate type.

- `pg_ddl_script(regclass) returns text`

    Extracts SQL DDL source of a class (table or view) `regclass`.
    This also includes all associated comments, ownership, constraints, 
    indexes, triggers, rules, grants, etc...

- `pg_ddl_script(regproc) returns text`
- `pg_ddl_script(regprocedure) returns text`

    Extracts SQL DDL source of function `regproc`.

- `pg_ddl_script(regtype) returns text`

    Extracts SQL DDL source for type `regtype`.

- `pg_ddl_script(regrole) returns text`

    Extracts SQL DDL definition for role (user or group) `regrole`.
    
There are two convenience functions to help you dump object without casting:

- `pg_ddl_script(oid) returns text`

    Extracts SQL DDL source for object ID, `oid`..

- `pg_ddl_script(text) returns text`

    Extracts SQL DDL source for a sql identifier`.

For example:

```sql
CREATE TABLE users (
    id int PRIMARY KEY,
    name text
);

SELECT pg_ddl_script('users'::regclass);

CREATE TYPE my_enum AS ENUM ('foo','bar');

SELECT pg_ddl_script('my_enum'::regtype);

SELECT pg_ddl_script(current_role::regrole);

```

A number of other functions are provided to extract more specific objects.
Their names all begin with `pg_ddl_`. They are used internally by the extension 
and are possibly subject to change in future versions of the extension. 
They are generally not intended to be used by the end user. 
Nevertheless, some of them are:

- `pg_ddl_identify(oid) returns table(oid oid, classid regclass, name name, namespace name, kind text, owner name, sql_kind text, sql_identifier text)`

    Identify an object by object ID, `oid`. This function is used a lot in others.

- `pg_ddl_create_table(regclass) returns text`

    Extracts SQL DDL source of a table.

- `pg_ddl_create_view(regclass) returns text`

    Extracts SQL DDL source of a view.

- `pg_ddl_create_class(regclass) returns text`

    Extracts SQL DDL source of a table or a view.

- `pg_ddl_create_function(regprocedure) returns text`

    Extracts SQL DDL source of a function.

See file `ddl.sql` for details.
