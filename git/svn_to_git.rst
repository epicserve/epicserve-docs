Convert SVN Repositories to Git Repositories
============================================

This guide on how to convert an SVN repository to a git repository was mostly taken from John Albin Wilkins post on `Converting a Subversion repository to Git <http://john.albin.net/git/convert-subversion-to-git>`_.

1. Retrieve a list of all Subversion committers
-----------------------------------------------

::

    $ svn log -q https://svn.example.com/repository_name | \
    awk -F '|' '/^r/ {sub("^ ", "", $2); sub(" $", "", $2); \
    print $2" = "$2" <"$2">"}' | sort -u > authors-transform.txt

That will grab all the log messages, pluck out the usernames, eliminate any duplicate usernames, sort the usernames and place them into a "authors-transform.txt” file. Now edit each line in the file. For example, convert::

    username = username <username>

into this::

    username = Firstname Lastname <username@example.com>

2. Clone the Subversion repository using git-svn
------------------------------------------------

::

    git svn clone [SVN repo URL] --no-metadata -A authors-transform.txt --stdlayout ~/temp

3. Convert svn:ignore properties to .gitignore
----------------------------------------------

If your svn repo was using svn:ignore properties, you can easily convert this to a `.gitignore` file using::

    cd ~/temp
    git svn show-ignore > .gitignore
    git add .gitignore
    git commit -m 'Convert svn:ignore properties to .gitignore.'

4. Push repository to a bare git repository
-------------------------------------------

First, create a bare repository and make its default branch match svn’s "trunk” branch name.

::

    git init --bare ~/new-bare.git
    cd ~/new-bare.git
    git symbolic-ref HEAD refs/heads/trunk

    cd ~/temp
    git remote add bare ~/new-bare.git
    git config remote.bare.push 'refs/remotes/*:refs/heads/*'
    git push bare

You can now safely delete the `~/temp` repository.

5. Rename "trunk" branch to "master"
------------------------------------

Your main development branch will be named "trunk” which matches the name it was in Subversion. You’ll want to rename it to Git’s standard "master” branch using::

    cd ~/new-bare.git
    git branch -m trunk master

6. Clean up branches and tags
-----------------------------

git-svn makes all of Subversions tags into very-short branches in Git of the form "tags/name”. You’ll want to convert all those branches into actual Git tags using::

    cd ~/new-bare.git
    git for-each-ref --format='%(refname)' refs/heads/tags |
    cut -d / -f 4 |
    while read ref
    do
      git tag "$ref" "refs/heads/tags/$ref";
      git branch -D "tags/$ref";
    done

7. Move bare repository to central remote repository
----------------------------------------------------

Example of how to move your local bare repository to a gitolite repository::

    $ mv new-bare.git repository_name.git
    $ tar czvf repository_name.git.tar.gz repository_name.git/
    $ scp repository_name.git.tar.gz remote_host:
    $ ssh remote_host
    $ tar xzvf repository_name.git.tar.gz
    $ sudo chown -R git:staff repository_name.git/
    $ cd repository_name.git/
    $ sudo find . -type f -exec chmod go= {} \;  # remove group and world permissions
    $ sudo find . -type d -exec chmod go= {} \;  # remove group and world permissions
    $ sudo su
    $ cd /Users/git/repositories/
    $ rm -rf repository_name.git/
    $ mv ~username/repository_name.git .

8. Clone new local copy
-----------------------

::

    mv old-svn-copy old-svn-copy.backup
    git clone git@remote_host:repository_name.git

List all unversioned files from your old local svn repository and copy them to the new local git repository::

    cd old-svn-copy.backup
    git clean -dXn  # Using this command because the old copy was a git-svn clone
    cp example-file.txt ../repository_name/  # copy all files and directories from the list that you need in the new local git repository


You can now move the local svn copy backup to your trash. It might be a good idea not to empty your trash until your sure everything is working correctly.

9. Done.
--------

