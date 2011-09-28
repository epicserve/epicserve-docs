SSH Key Notes
=============

Copying your public key to another computer::

    $ cat ~/.ssh/id_dsa.pub | ssh username@server.domain.tld "cat >> .ssh/authorized_keys"

Generating your keys on a new computer::

    $ ssh-keygen -t dsa
