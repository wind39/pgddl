Version 0.7
-----------
- pg_ddl_oid_info() renamed to pg_ddl_identify() and improved with new types
- pg_ddl_script() now works for oid and text arguments
- pg_ddl_script(oid) also handles constraints, triggers and defaults
- slight banner change
- use of format() function for speed and readability
- added pg_ddl_get_dependants() internal function
- aggregate support (not for ordered yet!)

Version 0.6
-----------
- support for dumping regroles
- support for collations on domains
- support for range types

Version 0.5
-----------
- support for foreign tables
- support for reloptions (alter view set)
- bugfix when printing [] before typmod

Version 0.4
-----------
- initial support for base types
- do not dump owner grants on classes
- use CREATE OR REPLACE in extension
- empty index name bug fix

Version 0.3
-----------
- support for column collations

Version 0.2
-----------
- bug fixes
- added META.json

Version 0.1
-----------
- initial version