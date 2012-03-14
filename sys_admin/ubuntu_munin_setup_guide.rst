Ubuntu Munin Setup Guide
========================

This guide originally borrowed from the `Linode Library <http://library.linode.com/server-monitoring/munin/ubuntu-11.04-natty>`_ with modifications and was originally setup for Ubuntu 11.10.


Installing Munin
----------------

Make sure your package repositories and installed programs are up to date by issuing the following commands::

    apt-get update
    apt-get upgrade --show-upgraded

The Munin system has two components: a master component often referred to as simply "munin," and a "node" component, or "munin-node," which collects the data and forwards it to the master node. In Lucid, you need to install both the munin and munin-node packages if you wish to monitor the Linode you plan to use as the primary Munin device. To install these packages, issue the following command::

    apt-get install munin munin-node

On all of the additional machines you administer that you would like to monitor with Munin, issue the following command::

    apt-get install munin-node

The machines that you wish to monitor with Munin do not need to run Ubuntu. The Munin project supports monitoring for a large number of operating systems. Consult the Munin project's `installation guide <http://munin.projects.linpro.no/wiki/Documentation>`_ for more information installing nodes on additional operating systems.


Configuring Munin
-----------------

Munin Master Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~

The master configuration file for Munin is ``/etc/munin/munin.conf``. This file is used to set the global directives used by Munin, as well as the hosts monitored by Munin. This file is large, so we've opted to show the key parts of the file. For the most part, the default configuration will be suitable to your needs.

The first section of the file contains the paths to the directories used by Munin. Note that these directories are the default paths used by Munin and can be changed by uncommenting and updating the path. When configuring your web server with Munin, make sure to point the root folder to the path of htmldir.

**File excerpt:** ``/etc/munin/munin.conf``

::

    # dbdir /var/lib/munin
    # htmldir /var/cache/munin/www
    # logdir /var/log/munin
    # rundir  /var/run/munin

There are additional directives after the directory location block such as ``tmpldir``, which shows Munin where to look for HTML templates, and others that allow you to configure mail to be sent when something on the server changes. These additional directives are explained more in depth on the `munin.conf page of the Munin website <http://munin.projects.linpro.no/wiki/munin.conf>`_. You can also find quick explanations inside the file itself via hash (#) comments. Take note that these global directives must be defined prior to defining hosts monitored by Munin. Do not place global directives at the bottom of the munin.conf file.

The last section of the ``munin.conf`` file defines the hosts Munin retrieves information from. For a default configuration, adding a host can be done in the form shown below:

**File:** ``/etc/munin/munin.conf``

::

    [example.org]
            address example.org

For more complex configurations, including grouping domains, see the comment section in the file, reproduced below for your convenience:

    **File:** ``/etc/munin/munin.conf``

::

    # A more complex example of a host tree
    #
    ## First our "normal" host.
    # [fii.foo.com]
    #       address foo
    #
    ## Then our other host...
    # [fay.foo.com]
    #       address fay
    #
    ## Then we want totals...
    # [foo.com;Totals] #Force it into the "foo.com"-domain...
    #       update no   # Turn off data-fetching for this "host".
    #
    #   # The graph "load1". We want to see the loads of both machines...
    #   # "fii=fii.foo.com:load.load" means "label=machine:graph.field"
    #       load1.graph_title Loads side by side
    #       load1.graph_order fii=fii.foo.com:load.load fay=fay.foo.com:load.load
    #
    #   # The graph "load2". Now we want them stacked on top of each other.
    #       load2.graph_title Loads on top of each other
    #       load2.dummy_field.stack fii=fii.foo.com:load.load fay=fay.foo.com:load.l$
    #       load2.dummy_field.draw AREA # We want area instead the default LINE2.
    #       load2.dummy_field.label dummy # This is needed. Silly, really.
    #
    #   # The graph "load3". Now we want them summarised into one field
    #       load3.graph_title Loads summarised
    #       load3.combined_loads.sum fii.foo.com:load.load fay.foo.com:load.load
    #       load3.combined_loads.label Combined loads # Must be set, as this is
    #                                                 # not a dummy field!
    #
    ## ...and on a side note, I want them listen in another order (default is
    ## alphabetically)
    #
    # # Since [foo.com] would be interpreted as a host in the domain "com", we
    # # specify that this is a domain by adding a semicolon.
    # [foo.com;]
    #       node_order Totals fii.foo.com fay.foo.com
    #

Munin Node Configuration
~~~~~~~~~~~~~~~~~~~~~~~~

The default ``/etc/munin/munin-node.conf`` file contains several variables you'll want to adjust to your preference. For a basic configuration, you'll only need to add the IP address of the master Munin server as a regular expression. Simply follow the style of the existing allow line if you're unfamiliar with regular expressions.

**File:** ``/etc/munin/munin-node.conf``

::

    # A list of addresses that are allowed to connect.  This must be a
    # regular expression, due to brain damage in Net::Server, which
    # doesn't understand CIDR-style network notation.  You may repeat
    # the allow line as many times as you'd like

    allow ^127\.0\.0\.1$

    # Replace this with the master munin server IP address
    allow ^123\.45\.67\.89$

The above line tells the munin-node that the master Munin server is located at IP address ``123.45.67.89``. After updating this file, restart the ``munin-node``. In Ubuntu, use the following command::

    /etc/init.d/munin-node restart


Web Interface Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can use Munin with the web server of your choice, simply point your web server to provide access to resources created by Munin. By default, these resources are located at ``/var/cache/munin/www``.

If you are using the Nginx HTTP Server you can create a Virtual Host configuration to serve the reports from Munin. In this scenario, we've created a subdomain in the DNS Manager and are now creating the virtual host file:

**File:** ``/etc/nginx/sites-available/stats.example.org``

::

    server {

        listen       80;
        server_name  stats.example.org;
        access_log   /var/log/nginx/stats.example.org.access.log;
        error_log    /var/log/nginx/stats.example.org.error.log;
        root         /var/cache/munin/www/;

        location = /favicon.ico {
            return  404;
        }

        location / {
            auth_basic            "Restricted";
            auth_basic_user_file  /etc/nginx/htpasswd;
            index  index.html;
        }

    }

If you haven't already setup the htpasswd file for controlling access to the site. Run the following command to encrypt a password. ::

    perl -le 'print crypt("my_secret_password", "salt")'

Use the encrypted password generated and add htpasswd file.

**File:** ``/etc/nginx/htpasswd``

::

    <username>:<encrypted_password>

Enable the new site and reload ``Nginx``::

    cd /etc/nginx/sites-enabled/
    sudo ln -s ../sites-available/stats.example.org
    sudo nginx -s reload



More Information
----------------

You may wish to consult the following resources for additional information on this topic. While these are provided in the hope that they will be useful, please note that we cannot vouch for the accuracy or timeliness of externally hosted materials.

- `Munin Homepage <http://munin.projects.linpro.no/>`_
- `Munin Exchange <http://muninexchange.projects.linpro.no/>`_
- `Installing Munin on Other Linux Distributions <http://munin.projects.linpro.no/wiki/LinuxInstallation>`_
- `Installing Munin on Mac OSX <http://munin.projects.linpro.no/wiki/DarwinInstallation>`_
- `Installing Munin on Solaris <http://munin.projects.linpro.no/wiki/SolarisInstallation>`_
