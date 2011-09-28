Debian Notes
============


Debian Packages
---------------

Note: To get emails sent to you automatically on what updates are available for your system, install apticron.

The ``dpkg`` command
~~~~~~~~~~~~~~~~~~~~

To list packages::

    $ dpkg -l

To list files in a package::

    $ dpkg -L <pkg_name>

The ``apt-cache`` command
~~~~~~~~~~~~~~~~~~~~~~~~~

To search all available packages::

    $ apt-cache search <pkg_name>

To find out more about a potential package::

    $ apt-cache showpkg <pkg_name>

The ``apt-get`` command
~~~~~~~~~~~~~~~~~~~~~~~

To update package database with latest avail versions::

    $ apt-get update

To upgrade all installed packages to latest versions::

    $ apt-get -u upgrade

Use this if it says the following packages where kept back::

    $ apt-get dist-upgrade

To install a particular package::

    $ apt-get install <pkg>

Uninstall package::

    $ apt-get remove <pkg>

The ``dpkg-reconfigure`` command
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To reconfigure a package if you config'd it wrong::

    $ dpkg-reconfigure <pkg>


Debian Services
---------------

Notes on services taken from http://www.shallowsky.com/linux/debnotes.html.

Enable service at boot time. (chkconfig svc on) ::

    $ update-rc.d svc defaults

Disable service at boot time. (chkconfig svc off)
``-f`` means force removal of the ``/etc/rc.?`` scripts while leaving the basic script in ``/etc/init.d`` (so you can change your mind later). ``--purge`` means remove the script from init.d. ::

    $ update-rc.d -f svc remove

Enable service at boot time in the given runlevels, like ``chkconfig svc --levels 2345 on`` ::

    $ update-rc.d svc start 20 2 3 4 5 . stop 20 0 1 6


Other Resources
---------------

- `Debian notes: doing things the "Debian Way" <http://www.shallowsky.com/linux/debnotes.html>`_
