Ubuntu Server Setup Guide for Django Websites
===============================================


This guide is a walk-through on how to setup Ubuntu Server for hosting Django websites. The Django stack that will be used in this guide is Ubuntu_, Nginx_, Gunicorn_ and Postgres_. This stack was chosen solely from the reading I've done and talking to other Django developers in order to get their recommendations. This stack seems to be one of the latest "standard" stacks for Django deployment. This guide also assumes that you're familiar with Ubuntu server administration and Django. I needed an example site for this guide so I chose to use my `Django Base Site <https://github.com/epicserve/django-base-site>`_ which is available on Github.

I would also like to thank `Ben Claar <https://twitter.com/#!/benclaar>`_, `Adam Fast <https://twitter.com/#!/adamcanfly>`_, `Jeff Triplett <https://twitter.com/#!/webology>`_ and `Frank Wiles <https://twitter.com/#!/fwiles>`_ for their suggestions and input on this guide.

.. _Ubuntu: http://www.ubuntu.com/business/server/overview
.. _Nginx: http://nginx.org/en/
.. _Gunicorn: http://gunicorn.org/
.. _Postgres: http://www.postgresql.org/


Step 1: Install Ubuntu Server
-----------------------------

The version of Ubuntu I'm using for this guide is `Ubuntu 11.10 64 bit Server <http://www.ubuntu.com/start-download?distro=server&bits=64&release=latest>`_. I've installed Ubuntu Server in a VirtualBox VM on my MacBook Pro which is currently running Mac OS X 10.7.2. During the installation of Ubuntu Server I answered the prompts with the following::

    Language: English
    Install Menu: Install Ubuntu Server
    Select a language: English
    Select your location: United States
    Configure the Keyboard: No
    Configure the keyboard: English (US)
    Configure the keyboard: English (US)
    Hostname: ubuntu-vm
    Configure the clock: Yes
    Partition disks: Guided - use entire disk and set up LVM
    Partition disks: SCSI3 (0,0,0) (sda) - 21.5 GB ATA VBOX HARDDISK
    Partition disks: Yes
    Partition disks: Continue
    Partition disks: Yes
    Set up users and passwords: Brent O'Connor
    Set up users and passwords: (Enter a username)
    Set up users and passwords: ********
    Set up users and passwords: ********
    Set up users and passwords: No
    Configure the package manager: <blank>
    Configure taskse1: No automatic updates
    Software selection: <Continue>
    Install the GRUB boot loader on a hard disk: Yes
    Installation complete: <Continue>


Step 2: Setup Port Forwarding
-----------------------------

Under the settings for your VM in VirtualBox click on the "Network" tab and then click on the "Port Forwarding" button. Now click on the plus and add the following settings to setup port forwarding for web and ssh.

+------------+------------+-----------+-----------+----------+------------+
| Name       | Protocol   | Host IP   | Host Port | Guest IP | Guest Port |
+============+============+===========+===========+==========+============+
| SSH        | TCP        |           | 2222      |          | 22         |
+------------+------------+-----------+-----------+----------+------------+
| Web        | TCP        |           | 8080      |          | 80         |
+------------+------------+-----------+-----------+----------+------------+

Step 3: Install Software
------------------------

OpenSSH
~~~~~~~

Since I like to connect to my servers using SSH the first thing I install is openssh-server::

    $ sudo aptitude install openssh-server

Since you setup port forwarding in step 2, you should now be able to open up your Terminal and connect to your Ubuntu Server using the following::

    $ ssh localhost -p 2222

Python Header Files
~~~~~~~~~~~~~~~~~~~

The Python header files are needed in order to compile binding libraries like ``psycopg2``. ::

    $ sudo aptitude install python2.7-dev

PostgreSQL
~~~~~~~~~~

::

    $ sudo aptitude install postgresql postgresql-server-dev-9.1

Make your Ubuntu user a PostgreSQL superuser::

    $ sudo su - postgres
    $ createuser --superuser <your username>
    $ exit

Restart PostgreSQL::

    $ sudo /etc/init.d/postgresql restart

Nginx
~~~~~

::

    $ sudo aptitude install nginx

Git
~~~

::

    $ sudo aptitude install git


Step 4: Setup a Generic Deploy User
-----------------------------------

The reason we are setting up a generic deploy user is so that if you have multiple developers who are allowed to do deployments you can easily add the developer's SSH public key to the deploy user's ``/home/deploy/.ssh/authorized_keys`` file in order to allow them to do deployments.

::

    $ sudo useradd -d /home/deploy -m -s /bin/bash deploy


Step 5: Install an Example Site
-------------------------------

Setup a virtualenv::

    $ sudo apt-get install python-setuptools
    $ sudo easy_install pip
    $ sudo pip install virtualenv
    $ cd /usr/local/
    $ sudo mkdir virtualenvs
    $ sudo chown deploy:deploy virtualenvs
    $ sudo su deploy
    $ cd virtualenvs
    $ virtualenv --no-site-packages example-site
    $ exit

.. note::

    I personally use and setup virtualenvwrapper on all my servers and local development machines so that I can use ``workon <virtualenv>`` to easily activate a virtualenv. This is why I put all my virtualenvs in ``/usr/local/virtualenvs``.


Make a location for the example site::

    $ cd /srv/
    $ sudo mkdir sites
    $ sudo chown deploy:deploy sites
    $ sudo su deploy
    $ cd sites
    $ git clone git://github.com/epicserve/django-base-site.git example-site
    $ cd example-site/
    $ echo `pwd` > /usr/local/virtualenvs/example-site/lib/python2.7/site-packages/django_project_root.pth
    $ mkdir -p static/cache
    $ exit
    $ sudo chown www-data:www-data /srv/sites/example-site/static/cache
    $ sudo su deploy

Create the file ``/srv/sites/example-site/config/settings/local.py`` and add the following. Make sure to change the password and then save the file. I usually use a `random string generator <http://clsc.net/tools/random-string-generator.php>`_ to generate a new password for each new Postgresql database and user::

    from base import *

    LOCAL_SETTINGS_LOADED = True

    DEBUG = True

    INTERNAL_IPS = ('127.0.0.1', )

    ADMINS = (
        ('Your Name', 'username@example.com'),
    )

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql_psycopg2',
            'NAME': 'example_site',
            'USER': 'example_site',
            'PASSWORD': '<enter a new secure password>',
            'HOST': 'localhost',
        }
    }

Install the sites required python packages::

    $ source /usr/local/virtualenvs/example-site/bin/activate
    $ cd /srv/sites/example-site/
    $ pip install -r config/requirements.txt

Install Gunicorn::

    $ pip install gunicorn

Install psycopg2::

    $ pip install psycopg2

Create a PostgreSQL user and database for your example-site::

    # exit out of the deploy user account
    $ exit
    $ createuser example_site -P
    $ Enter password for new role: [enter the same password you used in the local.py file from above]
    $ Enter it again: [enter the password again]
    $ Shall the new role be a superuser? (y/n) n
    $ Shall the new role be allowed to create databases? (y/n) y
    $ Shall the new role be allowed to create more new roles? (y/n) n
    $ createdb example_site -O example_site

Step 6: Daemonize Gunicorn using Ubuntu's Upstart
-------------------------------------------------

Create your Upstart configuration file::

    $ sudo vi /etc/init/gunicorn_example-site.conf

Add the following and save the file::

    description "upstart configuration for gunicorn example-site"

    start on net-device-up
    stop on shutdown

    respawn

    exec /usr/local/virtualenvs/example-site/bin/gunicorn_django -u www-data -c /srv/sites/example-site/config/gunicorn/example-site.py /srv/sites/example-site/config/settings/__init__.py

Start the gunicorn site::

    $ sudo start gunicorn_example-site


Step 7: Setup Nginx to proxy to your new example site
-----------------------------------------------------

Create a new file ``sudo vi /etc/nginx/sites-available/example-site.conf`` and add the following to the contents of the file::

    server {

        listen       80;
        server_name  localhost;
        access_log   /var/log/nginx/example-site.access.log;
        error_log    /var/log/nginx/example-site.error.log;

        location = /biconcave {
            return  404;
        }

        location  /static/ {
            root  /srv/sites/example-site/;
        }

        location  /media/ {
            root  /srv/sites/example-site/;
        }


        location  / {
            proxy_pass            http://127.0.0.1:8001/;
            proxy_redirect        off;
            proxy_set_header      Host             $host;
            proxy_set_header      X-Real-IP        $remote_addr;
            proxy_set_header      X-Forwarded-For  $proxy_add_x_forwarded_for;
            client_max_body_size  10m;
        }

    }

Enable the new site::

    $ cd /etc/nginx/sites-enabled
    $ sudo rm default
    $ sudo ln -s ../sites-available/example-site.conf

Start nginx::

    $ sudo /etc/init.d/nginx start


Step 8: Test the new example site
---------------------------------

While still connected to your Ubuntu server via SSH run the following, which should spit out the HTML for your site::

    wget -qO- 127.0.0.1:80

Since you setup port forwarding in step 2 for web, you should also be able to open up your browser on your local host machine and pull up the website using the URL, http://127.0.0.1:8080.


