Incron - Monitoring Linux File System Events
============================================

Install and Setup
-----------------

::

    $ sudo aptitude install incron

Add `root` to the config file `/etc/incron.allow` to allow incron to run as root. ::

    $ sudo echo "root" >> /etc/incron.allow


Example
-------

The following is an example of how you would setup *Incron* to watch a
directory for new files that have been uploaded and then trigger a script to
run with the full path to the file that was just uploaded in the directory
Incron is watching as the script's first command line arguement.

Run the following to edit the list of dirtories or files Incron is watching::

    $ sudo incrontab -e

Add something similar to following::

    /path/to/dirtory_to_watch IN_CLOSE_WRITE /path/to/script/my_script.py $@/$#

Save the file