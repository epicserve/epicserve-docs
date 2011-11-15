SFTP Chroot
===========

I've tested following SFTP Chroot setup on Ubuntu 11.10. It should be noted that the only way that I was able to give users write access to their root folder was to allow all sftp users the ability to see other sftp user root folders. Users won't be able to see other user's files in other users home directories, they will only be able to see what the name of other users top level home directory. I would be happier if I could find a configuration that works which only allowed sftp users to login to their home directory and only see their home directory.

Setup
-----

Make a directory where all your sftp chroot'ed users files will live::

    $ cd /srv
    $ sudo mkdir sftp
    $ sudo chmod 755 sftp

Make an `sftp` group::

    $ sudo groupadd sftp

Edit the sshd config file::

    $ sudo vi sshd_config

Comment out this line::

    # Subsystem sftp /usr/lib/openssh/sftp-server

Add this line directly below the line you just added::

    Subsystem sftp internal-sftp

At the bottom of the file add the following::

    Match group sftp
        X11Forwarding no
        ChrootDirectory /srv/sftp/
        AllowTcpForwarding no
        ForceCommand internal-sftp

Save the file::

    :wq

Reload ssh::

    $ sudo reload ssh


Add a SFTP User
---------------

Run the following which sets the new user's home directory to ``/srv/sftp/user`` and their shell to ``/bin/false`` so they can't login. ::

    $ sudo useradd -d /srv/sftp/user -s /bin/false user

Set the users group to ``sftp``::

    $ sudo usermod -g sftp user

Set the users password::

    $ sudo passwd user

Setup the users home directory::

    $ cd /srv/sftp
    $ sudo mkdir user
    $ sudo chown user:user user






