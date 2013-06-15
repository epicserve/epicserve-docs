Postgres Backup with Wal-e
==========================

Installation
------------

::

    $ sudo apt-get install libevent-dev python-all-dev daemontools lzop pv postgresql-client
    $ sudo pip install wal-e
    $ umask u=rwx,g=rx,o=
    $ mkdir -p /etc/wal-e.d/env
    $ echo "secret-key-content" > /etc/wal-e.d/env/AWS_SECRET_ACCESS_KEY
    $ echo "access-key" > /etc/wal-e.d/env/AWS_ACCESS_KEY_ID
    $ echo 's3://some-bucket/directory/or/whatever' > \
    /etc/wal-e.d/env/WALE_S3_PREFIX
    $ sudo chown -R root:postgres /etc/wal-e.d
