Mount a Windows File Share From the CLI
=======================================

The method that worked best for me::

    $ sudo mount -t cifs -o username=username //server/share /home/username/share/


Another method that might work in another situation::

    $ sudo mount -t smbfs -o username=username //server/share /mnt/share
