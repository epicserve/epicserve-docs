Ubuntu FTP Server Setup Guide
=============================

This guide will walk you through the steps necessary to setup and install a vsFTPd
FTP server for FTP only users with no shell login access and home directories
that are chrooted.

This guide was originally adapted from `Ubuntu's documentation <https://help.ubuntu.com/11.10/serverguide/C/ftp-server.html>`_.


Install and Setup vsFTPd
------------------------

Install vsFTPd using the following command::

    $ sudo apt-get install vsftpd

To allow users to upload files, edit ``/etc/vsftpd.conf`` and make sure the
following line is uncommented::

    write_enable=YES

Limit users to their home directories by uncommenting the following line in ``/etc/vsftpd.conf``::

    chroot_list_enable=YES

To allow users with a shell of ``/usr/sbin/nologin`` access to FTP, but have
no shell access, edit ``/etc/shells`` adding the nologin shell::

    # /etc/shells: valid login shells
    /bin/csh
    /bin/sh
    /usr/bin/es
    /usr/bin/ksh
    /bin/ksh
    /usr/bin/rc
    /usr/bin/tcsh
    /bin/tcsh
    /usr/bin/esh
    /bin/dash
    /bin/bash
    /bin/rbash
    /usr/bin/screen
    /usr/sbin/nologin

Add any users that you don't want to have FTP access to their files to
``/etc/ftpusers``. For security reasons I would add any of your regular system
users to this file. ::

    # /etc/ftpusers: list of users disallowed FTP access. See ftpusers(5).

    root
    daemon
    bin
    sys
    sync
    games
    man
    lp
    mail
    news
    uucp
    nobody
    my_local_user

Make a group that you will assign to all FTP only users::

    $ sudo groupadd ftp-users

Make a directory where all your FTP only users files will be located::

    $ sudo mkdir /home/ftp_users

Now restart vsftpd::

    $ sudo restart vsftpd


Create a FTP only user
----------------------

To create a FTP only user run the following, replacing ``my_user`` with the
username you would like to use. ::

    $ sudo useradd my_user \
    --home-dir /home/ftp_users/my_user \
    --gid ftp-users \
    --create-home \
    --no-user-group \
    --shell /usr/sbin/nologin; \
    sudo passwd my_user;






