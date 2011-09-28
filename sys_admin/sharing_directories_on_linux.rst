Sharing Directories on Linux
============================


Add a Group::

    $ sudo addgroup webadmin

Add an existing user to a group::

    $ sudo usermod -a -G webadmin myusername

Change an existing users primary group::

    $ sudo usermod -g webadmin myusername

View the a user's identity and primary group information::

    $ id myusername

Create the shared folder::

    $ mkdir myshareddir

Change the group owner for the group::

    $ chown -R myusername:webadmin myshareddir

Give write permissions to the group::

    $ chmod -R 775  myshareddir

Set an inherited group on files created within a directory, you can use the sgid bit on the directory permissions::

    $ chmod -R 2775 myshareddir

or... ::

    $ chmod -R g+s myshareddir

To make the default file permissions ``rw`` for group as well as user, you can change the ``umask`` to ``0002`` (either for all users in ``/etc/profile``, or for individual users in ``/etc/.bashrc``). Just append ``umask 0002`` to either of those files.