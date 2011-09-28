Redhat Notes
============

Redhat Packages
---------------

List any packages that need updating::

    $ up2date --list

Search for a package already installed::

    $ rqm -qa | grep foo

List all packages available for download::

    $ up2date --showall

Show all packages available that aren't installed::

    $ up2date --show-available

Install a package::

    $ up2date <package-name>

List package contents::

    $ rpm -lpq <package-name>


Redhat Services
---------------

List services and their runlevels::

    $ chkconfig --list

Turn a service on or off at boot::

    $ chkconfig <service> on|off


