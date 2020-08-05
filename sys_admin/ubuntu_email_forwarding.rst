Ubuntu - Setup Local Email Forwarding
=====================================

- Install Postfix Choosing "Internet Site" during the setup::

    $ sudo aptitude install postfix

- Setup your Aliases::

    $ sudo vi /etc/aliases

    # Add something like the following to the file
    postmaster:    root
    www-data: root
    root: myemail@example.com

    $ sudo newaliases

- Test your aliases::

    # You might have to install mailutils

    $ echo "Time sent: $(date +%Y%m%d-%H%M%S)" | mail root -s "Testing email on $(hostname -f)"

    # You should get an email in the inbox of whatever email you set root to forward to
