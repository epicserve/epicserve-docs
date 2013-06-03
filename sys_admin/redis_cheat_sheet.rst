Redis Cheat Sheet
=================

Connect to redis::

    $ redis-cli

Select a DB::

    redis 127.0.0.1:6379> SELECT 4

List all keys::

    redis 127.0.0.1:6379> KEYS *

Delete a key::

    redis 127.0.0.1:6379> DEL my_key_name

Script to list all key sizes::

    #!/usr/bin/env bash

    human_filesize() {
            awk -v sum="$1" ' BEGIN {hum[1024^3]="Gb"; hum[1024^2]="Mb"; hum[1024]="Kb"; for (x=1024^3; x>=1024; x/=1024) { if (sum>=x) { printf "%.2f %s\n",sum/x,hum[x]; break; } } if (sum<1024) print "1kb"; } '
    }

    redis_cmd='redis-cli'

    for k in `$redis_cmd keys "*"`; do key_size_bytes=`$redis_cmd debug object $k | perl -wpe 's/^.+serializedlength:([\d]+).+$/$1/g'`; size_key_list="$size_key_list$key_size_bytes $k\n"; done
    sorted_key_list=`echo -e "$size_key_list" | sort -n`
    echo -e "$sorted_key_list" | while read l; do size=`echo $l | perl -wpe 's/^(\d+).+/$1/g'`; hsize=`human_filesize "$size"`; key=`echo $l | perl -wpe 's/^\d+(.+)/$1/g'`; echo -e "$hsize\t\t$key"; done
