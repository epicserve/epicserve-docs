MySQL Admin Notes
=================

Backup all dbs::

    $ mysqldump --all-databases > all_databases.sql

Backup one db::

    $ mysqldump --opt db_name > backup-file.sql

Read a db dump back in to the database::

    $ mysql db_name < backup-file.sql

Create a new Database::

    $ mysqladmin -u root -p create databasename

Create a user for a database::

    mysql> GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE
    TEMPORARY TABLES, LOCK TABLES
    ON ee.*
    TO 'databasename'@'localhost' IDENTIFIED BY 'password';

Change password for root::

    mysql> use mysql;
    # The following is to list hostnames for root
    mysql> select Host, User, Password FROM user WHERE User = root;
    mysql> SET PASSWORD FOR 'root'@'hostname'=PASSWORD('my-password');
    mysql> flush privileges;
