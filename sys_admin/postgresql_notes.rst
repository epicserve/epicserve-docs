PostgreSQL Notes
================


Postgres Administration
-----------------------

Create a user::

    sudo su postgres
    postgres@hostname:~$ createuser
    Enter name of role to add: username
    Shall the new role be a superuser? (y/n) n
    Shall the new role be allowed to create databases? (y/n) n
    Shall the new role be allowed to create more new roles? (y/n) n
    CREATE ROLE

Create a DB::

    postgres@hostname:~$ createdb databasename
    CREATE DATABASE
    postgres@hostname:~$

    $ Grant access for the user to a DB
    postgres@hostname:~$ psql
    postgres=# alter user username with encrypted password 'password';
    ALTER ROLE
    postgres=# grant all privileges on database databasename to username;
    GRANT
    postgres@hostname:~$


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