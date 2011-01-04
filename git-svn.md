## Setup a New SVN Branch that tracks with a Local Git branch

First create the svn branch:

    $ svn cp https://svn.example.com/myproject/trunk https://svn.example.com/myproject/branches/new_experiment 

Fetch the new branch:

    $ git svn fetch

Create a new git branch that tracks with the new svn branch do the following. Note I've had trouble if you name the git branch with the same name as the svn branch so name it something different.

    $ git co -b git_new_experiment remotes/new_experiment

If you already have a git branch that you want to push up to new svn branch you can merge it into the branch you just created.

    $ git merge previous_new_experiment
    
Now you should be able to dcommit the new branch:

    $ git dcommit