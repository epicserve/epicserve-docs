Perl Search and Replace Examples
================================

Find all text files then back them up by making a copy with the ``.bak`` extention and then search for ``foo`` and replace it with ``bar``. ::

    $ find . -name '*.txt' | xargs perl -i.bak -wpe "s/foo/bar/g"

Example using a perl positive lookup assertion to avoid errors if the option isn't found. ::

    $ find . -name '*.php' | \
    xargs perl -i.bak -wpe 's/include((?:_once)?)\(INCLUDES?\. ?['\''"]?([^'\''^"^\)]+)['\''"]?\)/include$1('\''$2'\'')/g'
