Downloading a Sequence of Files
===============================

Example of how to download ``foo-01.txt`` through ``foo-17.txt``::

    $ for i in $(seq -f "%02g" 1 17); do wget http://example.com/foo-$i.txt; done
