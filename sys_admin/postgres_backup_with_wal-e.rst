Postgres Backup with Wal-e
==========================

The following are the steps I took to setup `Wal-e 0.6.2`_ on Ubuntu 12.04.2
LTS and Postgres 9.1.9. After following the installation instructions, every
minute Wal-e will make incremental backups to Amazon S3.

.. _Wal-e 0.6.2: https://github.com/wal-e/wal-e

Installation
------------

.. code-block:: bash

    $ sudo apt-get install libevent-dev python-all-dev daemontools lzop pv postgresql-client
    $ sudo pip install wal-e
    $ umask u=rwx,g=rx,o=
    $ mkdir -p /etc/wal-e.d/env
    $ echo "secret-key-content" > /etc/wal-e.d/env/AWS_SECRET_ACCESS_KEY
    $ echo "access-key" > /etc/wal-e.d/env/AWS_ACCESS_KEY_ID
    $ echo 's3://some-bucket/directory/or/whatever' > /etc/wal-e.d/env/WALE_S3_PREFIX
    $ sudo chown -R root:postgres /etc/wal-e.d

Added the following to the end of the file, `/etc/postgresql/9.1/main/postgresql.conf`:

.. code-block:: ini

    wal_level = archive
    archive_mode = on
    archive_command = 'envdir /etc/wal-e.d/env /usr/local/bin/wal-e wal-push %p'
    archive_timeout = 60

Restart postgres:

.. code-block:: bash

    $ sudo service postgresql restart

Make a base backup:

.. code-block:: bash

    $ sudo -u postgres bash -c "envdir /etc/wal-e.d/env /usr/local/bin/wal-e backup-push \
    /var/lib/postgresql/9.1/main"


Other Notes
-----------

You can use the following query (as superuser) to monitor how many segments
are 'ready' for archiving but have not yet been sent (those are 'done'). [#]_

.. code-block:: sql

    SELECT suffix, count(*)
        FROM (
            SELECT (regexp_matches(name, E'\[0-9A-F]+\.([^\.]*)$'))[1] AS suffix
            FROM pg_ls_dir('pg_xlog/archive_status') name
        ) AS matches
        GROUP BY suffix;


.. rubric:: Footnotes

.. [#] Thanks to `Daniel Farina <https://groups.google.com/d/msg/wal-e/QQYWDU5g7y0/Nq1fFOd5lqIJ>`_ for the tip.
