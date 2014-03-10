Ubuntu Security Guide
=====================

Good security resources that this is based off of ...

- `My First 5 Minutes On A Server; Or, Essential Security for Linux Servers <http://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers>`_
- `How To Set Up Your Linode For Maximum Awesomeness <http://feross.org/how-to-setup-your-linode/>`_

Setup UFW
---------

::

    sudo ufw allow from {your-ip} to any port 22
    sudo ufw allow http
    sudo ufw allow https
    sudo ufw enable
    sudo ufw logging off

Upgrade packages
----------------

::

    sudo aptitude update
    sudo aptitude upgrade

Install fail2ban
----------------

::

    sudo apt-get install fail2ban

Setup a deploy user
-------------------

::

    sudo useradd deploy
    sudo mkdir /home/deploy
    sudo mkdir /home/deploy/.ssh
    sudo chmod 700 /home/deploy/.ssh
    sudo chown -R deploy:deploy /home/deploy

Add your public key to the deploy and {your-username} user
----------------------------------------------------------

::

    sudo vim /home/deploy/.ssh/authorized_keys
    sudo vim /home/{your-username}/.ssh/authorized_keys
    sudo chmod 400 /home/deploy/.ssh/authorized_keys
    sudo chmod 400 /home/{your-username}/.ssh/authorized_keys

Lock down ssh
-------------

Edit ``/etc/ssh/sshd_config`` and change settings to match the following::

    PermitRootLogin no
    PasswordAuthentication no
    AllowUsers {your-username}@{your-ip} deploy@{your-ip}

Installed Logwatch
------------------
::

    sudo aptitude install logwatch

Change the execute line in ``/etc/cron.daily/00logwatch`` to ... ::

    /usr/sbin/logwatch --output mail --mailto {your-addy}@gmail.com --detail high

Enable Automatic Security Updates
---------------------------------
::

    sudo aptitude install unattended-upgrades

Edit ``/etc/apt/apt.conf.d/10periodic`` to look like::

    APT::Periodic::Update-Package-Lists "1";
    APT::Periodic::Download-Upgradeable-Packages "1";
    APT::Periodic::AutocleanInterval "7";
    APT::Periodic::Unattended-Upgrade "1";

Edit ``/etc/apt/apt.conf.d/50unattended-upgrades`` to look like::

    Unattended-Upgrade::Allowed-Origins {
            "${distro_id}:${distro_codename}-security";
    //      "${distro_id}:${distro_codename}-updates";
    //      "${distro_id}:${distro_codename}-proposed";
    //      "${distro_id}:${distro_codename}-backports";
    };

Setup the timezone
------------------

::

    dpkg-reconfigure tzdata
    ntpdate ntp.ubuntu.com

Edit `/etc/cron.daily` to match the following::

    #!/bin/bash

    ntpdate ntp.ubuntu.com

Change the files permissions::

    sudo chmod 755 /etc/cron.daily/ntpdate