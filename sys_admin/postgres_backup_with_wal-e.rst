Postgres Backup with Wal-e
==========================

.. warning::

    This is currently a work in progress and isn't to be trusted yet.

The following are the steps I took to setup Wal-e on Ubuntu 12.04.2 LTS and Postgres 9.1.9.

Installation
------------

::

    $ sudo apt-get install libevent-dev python-all-dev daemontools lzop pv postgresql-client
    $ sudo pip install wal-e
    $ umask u=rwx,g=rx,o=
    $ mkdir -p /etc/wal-e.d/env
    $ echo "secret-key-content" > /etc/wal-e.d/env/AWS_SECRET_ACCESS_KEY
    $ echo "access-key" > /etc/wal-e.d/env/AWS_ACCESS_KEY_ID
    $ echo 's3://some-bucket/directory/or/whatever' > /etc/wal-e.d/env/WALE_S3_PREFIX
    $ sudo chown -R root:postgres /etc/wal-e.d

Added the following to the end of the file, `/etc/postgresql/9.1/main/postgresql.conf`::

    wal_level = archive
    archive_mode = on
    archive_command = 'envdir /etc/wal-e.d/env /usr/local/bin/wal-e wal-push %p'
    archive_timeout = 60

Restart postgres::

    $ sudo service postgresql restart

Make a base backup::

    $ sudo -u postgres bash -c "envdir /etc/wal-e.d/env /usr/local/bin/wal-e backup-push /var/lib/postgresql/9.1/main"
