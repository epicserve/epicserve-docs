Monit Notes
===========

Installing
----------

::

    $ sudo aptitude install monit

Setup Alerting
--------------

Add the following to the `/etc/monit/conf.d/global_settings` file::

    set mailserver localhost
    set alert sysadmin@company.com

Reload monit `sudo service monit reload`

Setup Basic System Alerts
-------------------------

Add the following to the `/etc/monit/conf.d/system_checks` file::

    check system localhost
        if memory usage > 85% then alert
        if cpu usage (user) > 80% for 3 cycles then alert
        if cpu usage (system) > 80% for 3 cycles then alert

    check filesystem rootfs with path /
        if space usage > 80% then alert

Reload monit `sudo service monit reload`
