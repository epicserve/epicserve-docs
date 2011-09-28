Sed Examples
============

Examples of how to use sed to search and replace text inside of text files. ::

    $ find . -name '*.php' | xargs sed -i "s/\$cfg\['siteurl'\]/SITE_URL/g"
    $ find . -name '*.php' | xargs sed -i 's/SITE_URL}\//SITE_URL ."/g'
    $ find . -name '*.php' | xargs sed -i "s/\$cfg\['public_site'\]/PUBLIC_SITE_URL/g"
    $ find . -name '*.php' | xargs sed -i 's/"{INCLUDES}/INCLUDES . "/g'
    $ find . -name '*.php' | xargs sed -i "s/\$cfg\['photo_batch_upload_dir'\]/PHOTO_BATCH_UPLOAD_DIR/g"
    $ find . -name '*.php' | xargs sed -i "s/\$cfg\['photo_thumbs'\]/PHOTO_THUMBS/g"
    $ find . -name '*.php' | xargs sed -i "s/\$cfg\['photo_large'\]/PHOTO_LARGE/g"
    $ find . -name '*.php' | xargs sed -i "s/\$cfg\['photo_orig'\]/PHOTO_ORIG/g"
    $ find . -name '*.php' | xargs sed -i "s/\$cfg\['photo_thumbs_url'\]/PHOTO_THUMBS_URL/g"
    $ find . -name '*.php' | xargs sed -i "s/\$cfg\['photo_medium_url'\]/PHOTO_MEDIUM_URL/g"
    $ find . -name '*.php' | xargs sed -i "s/\$cfg\['photo_orig_url'\]/PHOTO_ORIG_URL/g"

Example of how to move your backup files (``.bak``), back to the origional file::

    $ for x in `find -name \*.php.bak | sed -re 's/(.*).bak/\1/'`; \
    do mv -i -f ${x}.bak ${x}; done
