PostgreSQL Notes
================


Postgres Administration
-----------------------

Postgres Shell
~~~~~~~~~~~~~~

Run the following to access the Postgres shell::

    $ psql -d template1
    template1=#

User Management
~~~~~~~~~~~~~~~

Create a user::

    $ createuser dbusername
    Shall the new role be a superuser? (y/n) n
    Shall the new role be allowed to create databases? (y/n) n
    Shall the new role be allowed to create more new roles? (y/n) n

Create a user with a password and no privileges::

    $ createuser -DRSW dbusername

List all users::

    template1=# select usename from pg_user;

Delete a user::

    $ dropuser dbusername

Database Management
~~~~~~~~~~~~~~~~~~~

Create a database::

    $ createdb dbname -O dbusername

Delete a database::

    template1=# drop database <db_name>;

List all databases::

    template1=# select datname from pg_database;

List all databases sizes::

    template1=# SELECT pg_database.datname, pg_size_pretty(pg_database_size(pg_database.datname)) AS size FROM pg_database;

List the size of each table in a database::

    db_name=# SELECT relname, reltuples, relpages * 8 / 1024 AS "MB" FROM pg_class ORDER BY relpages DESC;

Helpful Queries
---------------

List the number of concurrent connections::

    template1=# select * from pg_stat_activity;

Sync a Production Postgres DB with a local development DB
---------------------------------------------------------

Open up terminal and do the following::

    $ ssh remote_host
    $ pg_dump -U db_user db_name > db_name.sql

Open a new terminal tab for localhost and do the following::

    $ dropdb db_name
    $ createdb db_name -O db_user
    $ psql -U db_user -d db_name < db_name.sql

Switch back to the remote host tab and do the following::

    $ rm ads.sql


Common Postgres Shell Commands
------------------------------

View table structure::

    postgres=> \d <table name>

To add a column to a table::

    ALTER TABLE products ADD COLUMN description text;

To remove a not-null constraint::

    ALTER TABLE products ALTER COLUMN product_no DROP NOT NULL;

To remove any default value, use::

    ALTER TABLE products ALTER COLUMN price DROP DEFAULT;

Output a SELECT query to a csv file::

    open terminal
    su postgres
    psql <db_name>
    \f ','
    \a
    \t
    \o outputfile.csv
    select ..... (your sql select statement)
    \o
    \q
