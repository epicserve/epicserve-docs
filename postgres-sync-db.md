# Sync a Production Postgres DB with a local dev db

Open up terminal and do the following:

    $ ssh remote_host
    $ pg_dump -U db_user db_name > db_name.sql
    
Open a new terminal tab for localhost and do the following:

    $ dropdb db_name
    $ createdb db_name -O db_user
    $ psql -U db_user -d db_name < db_name.sql

Switch back to the remote host tab and do the following:

    $ rm ads.sql