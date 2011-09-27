SVN Notes
=========

Creating a svn repository
-------------------------

Create the svn repository where all the files and different versions will be kept::

    svnadmin create /www/svn/myproject


Add directories for your main trunk and tags, and branches::

    sudo svn mkdir file:///www/svn/myproject/trunk \
    file:///www/svn/myproject/branches \
    file:///www/svn/myproject/tags \
    -m "Adding standard subversion directory structure"

Double check your repository by listing what is in your repository::

    svn list file:///www/svn/myproject/

Look at your repository info::

    svn info file:///www/svn/myproject/

Import an existing project into your repository::

    cd /www/myproject/
    cd ..
    svn import myproject/ file:///www/svn/myproject/trunk -m "Importing my project"

Double check your repository::

    svn list file:///www/svn/myproject/trunk

Check out your repository to the working directory::

    rm -rf /www/myproject/*
    cd /www/myproject/
    svn co file:///www/svn/myproject/trunk .


Working with your checked out copy
----------------------------------

Add a file and commit it to the repository::

    cd /www/myproject
    touch my-test-file.txt
    svn add my-test-file.txt
    svn commit . -m "Added the my-test-file.txt"

Rename a file and commit changes::

    cd /www/myproject
    svn mv my-test-file.txt my-test-file2.txt
    svn commit . -m "Renamed my-test-file.txt to my-test-file2.txt"

Delete a file and commit changes::

    cd /www/myproject
    svn rm my-test-file2.txt
    svn commit . -m "Deleted my-test-file2.txt"

List the changes locally and in the repository::

    svn status -u

Making tags or branches::

    svn copy -m "Notes about the project, r<revision-number-before-change>"
    file:///www/svn/myproject/trunk
    file:///www/svn/myproject/tags/before-some-major-change

To switch your working copy to the new branch you just made::

    svn switch \
    file:///www/svn/myproject/tags/before-some-major-change

Merging changes from another branch or trunk
--------------------------------------------

Before merging you should commit any of your current changes so that you can
revert back to your current state if the merge doesn't go well.

Go to your working copy and then use the version numbers one above the one
where you made the changes you want to merge in and then use the last revision
number where you made the changes. Then put the path to repository you are
merging from. After you run the following command it shows what changes it
made to your working copy.  Make sure it made the correct changes and if it
did you can then commit those changes to the branch or trunk that your current
working copy is checked out from.

Example::

    svn merge -r 20:22 file:///www/svn/cms/trunk

If you mess up you can always run ``svn revert --recursive .``

Using svn over ssh
------------------

If you want to checkout your repository from another computer you can use::

    svn+ssh://hostname/path/to/repos instead of file:///path/to/repos/

Undoing a bad commit
--------------------

If you make a commit and it's reverse number was 4 and you want to undo the bad commit you can do the following in your local copy.

::

    svn merge -r 4:3 file:///www/svn/project/trunk
