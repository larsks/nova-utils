Nova utils
==========

This is a collection of misc. utilities designed to make it easier to
interact with OpenStack from shell scripts.

The scripts
===========

nova-list-ips
-------------

`nova-list-ips` will list all instances visible to you and their ip
addresses in a form amendable to `awk`-ing.  For example:

    lars-node-0 fixed_0 4 172.16.10.41
    lars-node-3 fixed_0 4 172.16.10.40
    lars-node-2 fixed_0 4 172.16.10.45
    lars-node-1 fixed_0 4 172.16.10.46
    lars-master fixed_0 4 172.16.10.39
    lars-master fixed_0 4 10.243.28.25
    lars-master fixed_0 4 10.243.28.34

The fields are `name`, `network`, `protocol`, and `address`.

nova-wait-active
----------------

Given a list of instance names or ids, wait until all the instances
are active.  This can be used to boot a number of instances in
parallel and then wait for them all to become available, rather than
booting them serially using `--poll`.  For example:

    echo "Booting nodes."
    for x in 0 1 2 3; do
      echo "  Booting node $x."
      nova boot --image=fedora-17-x86_64-minimal \
        --flavor=m1.small \
        lars-node-$x > /dev/null 2>&1 &
      server_names="$server_names lars-node-$x"
    done

    # Wait for boot processes to exit.

    echo "Waiting for instances."
    wait
    ./bin/nova-wait-active $server_names

