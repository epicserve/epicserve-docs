IPtables Notes
==============

To reset iptables::

    $ iptables -t filter -P INPUT ACCEPT
    $ iptables -t filter -P OUTPUT ACCEPT
    $ iptables -t filter -P FORWARD ACCEPT
    $ iptables -F

To list current chains::

    $ iptables -L
    $ iptables iptables -t nat -n -L

To load your iptables config file::

    $ iptables-restore /etc/network/iptables

To save your current chains that are in memory::

    $ iptables-save > /etc/network/iptables
