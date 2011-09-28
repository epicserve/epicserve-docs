Sudo Notes
==========

Add an admin to sudo::

    $ sudo usermod -G admin username

If they are members of other groups::

    $ sudo usermod -a -G admin username
