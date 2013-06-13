Monit Notes
===========

`Monit <http://mmonit.com/monit/>`_ is a good service for sending alerts and restart services based on simple rules.

Installing
----------

::

    $ sudo aptitude install monit

Setup Alerting
--------------

Add the following to the `/etc/monit/conf.d/global_settings` file::

    set mailserver localhost
    set alert sysadmin@company.com

    ## Uncomment the following if you don't want alerts every time you reload or
    ## restart monit.
    #
    # check system localhost
    #     alert sysadmin@company.com but not on { instance }

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

Monitoring Gunicorn Example
---------------------------

Add the following to the `/etc/monit/conf.d/gunicorn` file::

    check process gunicorn with pidfile /var/run/gunicorn_www.example.com.pid
        start program = "/sbin/start gunicorn_www.example.com"
        stop program  = "/sbin/stop gunicorn_www.example.com"
        if failed unixsocket /tmp/gunicorn_www.example.com.sock then start
        if totalcpu > 20% for 1 cycles then alert
        if totalmemory usage > 60% then restart

Reload monit `sudo service monit reload`

.. note::

    Make sure you use `totalcpu` and `totalmemory`, because you want to make
    sure it totals the memory and cpu usage for all child processes.

    Also, the name of the process doesn't seem to matter. In the above example
    I could have used "`check process django_gunicorn`" if I wanted instead of
    "`check process gunicorn`." The name you use is what will be used in alert
    emails.
