## Setup a New SVN Branch that tracks with a Local Git branch

See the follow for docs, http://stackoverflow.com/questions/266395/git-svn-how-do-i-create-a-new-svn-branch-via-git

However this is what worked for me...

    $ git svn branch -m "Branch for new feature" new_feature
    $ git co -b local_new_feature remotes/new_feature