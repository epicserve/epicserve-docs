General Git Notes
=================

Good Basic Global Git Config Settings
-------------------------------------

::

    [color]
        diff = auto
        status = auto
        branch = auto
    [user]
        name = Your Name
        email = yourname@gmail.com
    [core]
        excludesfile = /Users/username/.gitignore
        editor = mate
    [alias]
        st = status
        d = diff
        ci = commit -v
        cia = commit -v -a
        co = checkout
        cp = cherry-pick
        dci = svn dcommit
        l = log
        ll = log -p
        lm = log master..
        llm = log -p master..
        b = branch

Starting a Git repository
-------------------------

::

    cd project/
    git init                  # initializes the repository
    git add -n .              # do a dry-run to see what file are going to be added so you can make sure all the files you want to be ignored are ignored.
    git add .                 # add those 'unknown' files
    git rm --cached <file>... # use this command to remove any files that where added that you didn't want added
    git commit                # commit all changes, edit changelog entry

Add the a Remote and Push
-------------------------

::

    git remote add origin git@<server-domain-name>:<repository-name>.git
    git push origin master

If you want to be able to automatically pull from your remote repository you need to run the following command.

::

    git branch --set-upstream master origin/master

The previous command adds the following to your `.git/config` for your current project.

::

    [branch "master"]
        remote = origin
        merge = refs/heads/master

Ignoring Files
--------------

To add files for just the current project use::

    cd project/
    touch .gitignore
    echo "path/to/file" >> .gitignore

To add ignore rules for every project just add the `.gitignore` file to your home directory `~/`.

Ways to Remove a file that was added before committing
------------------------------------------------------

::

    git rm --cached <file>
    git reset HEAD <file>

Setup a remote repository
-------------------------

Set up the new bare repo on the server::

    $ ssh myserver.com
    $ mkdir /var/git/myapp.git && cd /var/git/myapp.git
    $ git --bare init

Add the remote repository to your existing local git repository and push::

    $ cd project/
    $ git remote add origin ssh://myserver.com/var/git/myapp.git
    $ git push origin master


Setup a git repository from a Subversion repository
---------------------------------------------------

::

     git svn clone -s http://myserver.com/path/to/svn/repository repository
     cd repository
     git branch -a
     git reset --hard remotes/trunk

Push Changes back up to SVN
---------------------------

Checkout a branch and make edits::

    git checkout -b my_branch

Make your edits and commit changes::

    git add .
    git commit -m "Made some changed to the branch"

Update against any changes made to your svn repo, rebase and then commit changes to your svn repo::

    git svn dcommit

Start and Track a New Remote Branch
-----------------------------------

Create the new remote branch::

    git push origin master:newbranch

Setup the new remote branch locally so you can push and pull to the new remote branch::

    git checkout --track -b newbranch origin/newbranch

Delete a remote Branch
----------------------

::

    git push origin :mybranch

Share Uncommitted Changes
-------------------------

To share uncommitted changes with someone or if you want to copy uncommitted changes from one git repository to another, first generate a patchfile from the git repository that has the changes you want to share.

::

    git diff --no-prefix > patchfile

Copy the patchfile to the computer with the git repository that you want to apply the changes too and then run the following command.

::

     patch -p0 < patchfile

Any untracked files will have to be copied manually.
