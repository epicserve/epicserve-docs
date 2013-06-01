Redis Notes
===========

If you're having issues with Redis crashing, *check the logs* (/var/log/redis/redis-server.log) because you might find helpful notes like ...

::

    # WARNING overcommit_memory is set to 0! Background save may fail under low memory
    # condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf
    # and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to
    # take effect.

Doing what it suggested fixed my issue.