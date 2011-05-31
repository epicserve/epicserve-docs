
## Changing your Git svn commit url

Tried the following method that I originally got from, http://translate.org.za/blogs/wynand/en/content/changing-your-svn-repository-address-git-svn-setup and couldn't get it to work. I resorted to checking out the repository again and then ran `git clean -dXn` to see what files in the old copy that I needed to copy into the new copy of the repository.


1. Back up your git repo. If you make a mistake with repository rewriting you'll be in for a lot of fun.
2. git gc
3. git filter-branch --msg-filter "sed 's|git-svn-id: http://svn.wenworld.com|git-svn-id: https://svn.wenatcheeworld.com|g' $(cat .git/packed-refs | awk '// {print $2}' | grep -v 'pack-refs')"
4. rm -rf .git/svn
5. edit .git/config and change "http://svn.wenworld.com" in all the git-svn URLs to "https://svn.wenatcheeworld.com"
6. git svn rebase (to update your repo and to let the git-svn data be rebuilt)