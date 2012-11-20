Command Linux Commands
======================


Files Commands
--------------

list sub directories::

    $ ls -la | grep ^d

To watch a file as it changes::

    $ tail -f /path/to/file

To check what directories are taking the most space::

    $ du -xm / | sort -n | tail -50

Check what devices are taking the most space::

    $ ls -lrSh /dev | tail -50

Do a grep regex search::

    $ find . -name "*.xml" | xargs grep -Pzo "(?s)<pub10>.*?</pub10>"

Computer Information
--------------------

Print operating system name::

    uname -a


Network Commands
----------------

View tcp connection for an ip address::

    $ tcpdump -x host 192.168.0.1


Common Tar Commands
-------------------

Extract the contents of example.tar and display it's contents::

    $ tar xvf example.tar

Extract and uncompress a compressed tar archive::

    $ tar xzvf example.tar.gz

Create a tar archive of a directory::

    $ tar -cf example.tar /path/to/example

Create a tar archive and of a directory and compress the directory::

    $ tar -czvf example.tar.gz /path/to/example

View the contents of a tar archive::

    $ tar -tvf example.tar


How to send other users messages from the terminal
--------------------------------------------------

::

    $ write username tty
    message text
    CTRL+C


Process Commands
----------------

List all processes::

    $ ps -ef

Another way to list all processes::

    $ ps -aux

List Process Tree::

    $ ps -axf


Search and Replace Text in Text Files
-------------------------------------

Search for all python files and replace foo with bar::

    $ find . -name "*.py" | xargs perl -i.bak -wpe 's/\bfoo\b/bar/g'

Command to move your files back to the original if something went back with the search and replace::

    $ for i in *txt;do cp ${i}.bak $i;done

Another way to move your backup files back to the original if something went bad with the search and replace::

    $ find . -iname '*.txt' -exec mv {}.bak {} \;


Rename files in a batch
-----------------------

Rename files by number::

    $ c=0; for i in *.jpg; do (( c++ )); mv "$i" "$c".jpg; done

Change a files extention::

    $ for i in *.txt; do mv $i ${i/.txt/.doc}; done
