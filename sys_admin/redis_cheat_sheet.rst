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
