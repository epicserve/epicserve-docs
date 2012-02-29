MySQL Admin Notes
=================


Database Administration
-----------------------

Backup all dbs::

    $ mysqldump --all-databases | gzip > all_databases.sql.gz

Backup one db::

    $ mysqldump --opt db_name | gzip > backup-file.sql.gz

Read a db dump back in to the database::

    $ gunzip < backup-file.sql.gz | mysql db_name

Create a new Database::

    $ mysqladmin -u root -p create databasename

Rename a database::

    $ mysqldump -u root -p old_db_name > old_db_name.sql
    $ mysqladmin -u root -p create new_db_name
    $ mysql -u root -p new_db_name < old_db_name.sql
    $ mysql -u root -p -e "DROP DATABASE old_db_name"


User Administration
-------------------

Create a user for a database::

    mysql> GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE
    TEMPORARY TABLES, LOCK TABLES
    ON db_name.*
    TO 'user_name'@'localhost' IDENTIFIED BY 'password';

Change the root password::

    mysql> use mysql;
    # The following is to list hostnames for root
    mysql> select Host, User, Password FROM user WHERE User = root;
    mysql> SET PASSWORD FOR 'root'@'hostname'=PASSWORD('my-password');
    mysql> flush privileges;
