.. _doc_nsd_configuration:

Configuration
-------------

NSD has a vast array of configuration options for advanced use cases. To
configure the application, a ``nsd.conf`` configuration file used. The file
format has attributes and values, and some attributes have attributes inside
them. 

The basic principles are:

  - The used notation is ``attribute: value``
  - Comments start with ``#`` and extend to the end of a line
  - Empty lines are ignored as is whitespace at the beginning of a line
  - Quotes can be used, for names containing spaces, e.g. ``"file name.zone"``
  
``nsd.conf`` specifies options for the NSD server, zone files, primaries and
secondaries. Here is an example:

.. code:: text

       # Example.com nsd.conf file
       # This is a comment.

       server:
            server-count: 1 # use this number of cpu cores
            database: ""  # or use "/var/db/nsd/nsd.db"
            zonelistfile: "/var/db/nsd/zone.list"
            username: nsd
            logfile: "/var/log/nsd.log"
            pidfile: "/var/run/nsd.pid"
            xfrdfile: "/var/db/nsd/xfrd.state"

       zone:
            name: example.com
            zonefile: /etc/nsd/example.com.zone

       zone:
            # this server is master, 192.0.2.1 is the secondary.
            name: masterzone.com
            zonefile: /etc/nsd/masterzone.com.zone
            notify: 192.0.2.1 NOKEY
            provide-xfr: 192.0.2.1 NOKEY

       zone:
            # this server is secondary, 192.0.2.2 is master.
            name: secondzone.com
            zonefile: /etc/nsd/secondzone.com.zone
            allow-notify: 192.0.2.2 NOKEY
            request-xfr: 192.0.2.2 NOKEY
=======
Before running NSD you need to create a configuration file for it. The config
file contains server settings, secret keys and zone settings. In the repository
we provide a `sample configuration
<https://github.com/NLnetLabs/nsd/blob/master/nsd.conf.sample.in>`_ to get
started.

The server settings start with a line with the keyword ``server:``. In the
server settings set ``database: <file>`` with the filename of the name database
that NSD will use. Set ``chroot: <dir>`` to run NSD in a chroot-jail. Make sure
the zone files, database file, xfrdfile, difffile and pidfile can be accessed
from the chroot-jail.  Set ``username: <user>`` to an  unprivileged user, for
security.  

For example:

.. code-block:: text

  # This is a sample configuration
  server:
    database: "/etc/nsd/nsd.db"
    pidfile: "/etc/nsd/nsd.pid"
    chroot: "/etc/nsd/"
    username: nsd

After the global server settings to need to make entries for the
zones that you wish to serve. For each zone you need to list the zone
name, the file name with the zone contents, and access control lists.

.. code-block:: text

  zone:
    name:	"example.com"
    zonefile: "example.com.zone"

The zone file needs to be filled with the correct zone information for primary
zones. For secondary zones an empty file will suffice, a zone transfer will be
initiated to obtain the secondary zone contents.

Access control lists are needed for zone transfer and notifications.

For a secondary zone list the masters, by IP address. Below is an example
of a secondary zone with two primary servers. If a primary only supports AXFR
transfers and not IXFR transfers (like NSD), specify the primary as
``request-xfr: AXFR <ip_address> <key>``. By default, all zone transfer requests 
are made over TCP. If you want the IXFR request be transmitted over UDP, use
``request-xfr: UDP <ip address> <key>``.

.. code-block:: text

  zone:
    name: "example.com"
    zonefile: "example.com.zone"
    allow-notify: 168.192.185.33 NOKEY
    request-xfr: 168.192.185.33 NOKEY
    allow-notify: 168.192.199.2 NOKEY
    request-xfr: 168.192.199.2 NOKEY

By default, a secondary will fallback to AXFR requests if the primary told us it
does not support IXFR. You can configure the secondary not to do AXFR fallback
with:

.. code-block:: text

    allow-axfr-fallback: "no"

For a primary zone, list the secondary servers, by IP address or subnet. Below
is an example of a primary zone with two secondary servers:

.. code-block:: text

  zone:
    name: "example.com"
    zonefile: "example.com.zone"
    notify: 168.192.133.75 NOKEY
    provide-xfr: 168.192.133.75 NOKEY
    notify: 168.192.5.44 NOKEY
    provide-xfr: 168.192.5.44 NOKEY

You also can set the outgoing interface for notifies and zone transfer requests 
to satisfy access control lists at the other end:

.. code-block:: text

    outgoing-interface: 168.192.5.69

By default, NSD will retry a notify up to five times. You can override that
value with: 

.. code-block:: text

    notify-retry: 5

Zone transfers can be secured with TSIG keys, replace NOKEY with the name of the
TSIG key to use. See :ref:`Using TSIG <doc_nsd_tsig>` for details.

Since NSD is written to be run on the root name servers, the config file  can to
contain something like:

.. code-block:: text

  zone:
    name: "."
    zonefile: "root.zone"
    provide-xfr: 0.0.0.0/0 NOKEY # allow axfr for everyone.
    provide-xfr: ::0/0 NOKEY

You should only do that if you're intending to run a root server, NSD is not
suited for running a ``.`` cache. Therefore if you choose to serve the ``.``
zone you have to make sure that the complete root zone is timely and fully
updated.

To prevent misconfiguration, NSD configure has the
:option:`--enable-root-server` option, that is by default disabled.

In the config file, you can use patterns. A pattern can have the same
configuration statements that a zone can have.  And then you can
``include-pattern: <name-of-pattern>`` in a zone (or in another pattern) to
apply those settings. This can be used to organise the settings.

The :command:`nsd-control` tool is also controlled from the ``nsd.conf`` config
file. It uses TLS encrypted transport to 127.0.0.1, and if you want to use it
you have to setup the keys and also edit the config file.  You can leave the
remote-control disabled (the secure default), or opt to turn it on:

.. code-block:: text

    # generate keys
    nsd-control-setup

.. code-block:: text

  # edit nsd.conf to add this
  remote-control:
    control-enable: yes

By default :command:`nsd-control` is limited to localhost, as well as encrypted,
but some people may want to remotely administer their nameserver.  What you then
do is setup :command:`nsd-control` to listen to the public IP address, with
``control-interface: <IP>`` after the control-enable statement. 

Furthermore, you copy the key files :file:`/etc/nsd/nsd_server.pem`
:file:`/etc/nsd/nsd_control.*` to a remote host on the internet; on that host
you can run :command:`nsd-control` with :option:`-c <special config file>` which
references same IP address ``control-interface`` and references the copies of
the key files with ``server-cert-file``, ``control-key-file`` and
``control-cert-file`` config lines after the ``control-enable`` statement.  The
nsd-server authenticates the nsd-control client, and also the
:command:`nsd-control` client authenticates the nsd-server.

When you are done with the configuration file, check the syntax using

.. code-block:: text

    nsd-checkconf <name of configfile>

The zone files are read by the daemon, which builds :file:`nsd.db` with their
contents. You can start the daemon with:

.. code-block:: text

    nsd
    or with "nsd-control start" (which execs nsd again).
    or with nsd -c <name of configfile>

To check if the daemon is running look with :command:`ps`, :command:`top`, or if
you enabled command:`nsd-control`:

.. code-block:: text

    nsd-control status

To reload changed zone files after you edited them, without stopping the daemon,
use this to check if files are modified: 

.. code-block:: text

    kill -HUP `cat <name of nsd pidfile>`

If you enabled :command:`nsd-control`, you can re-read with:

.. code-block:: text

    nsd-control reload
    
With :command:`nsd-control` you can also reread the config file, in case of new
zones, etc.

.. code-block:: text

    nsd-control reconfig

To restart the daemon:

.. code-block:: text

    /etc/rc.d/nsd restart  # or your system(d) equivalent

To shut it down (for example on the system shutdown) do:

.. code-block:: text

    kill -TERM <pid of nsd>
    or nsd-control stop

NSD will automatically keep track of secondary zones and update them when
needed. When primary zones are updated and reloaded notifications are sent to
secondary servers.

The zone transfers are applied to :file:`nsd.db` by the daemon.  To write
changed contents of the zone files for secondary zones to disk in the text-based
zone file format, issue :command:`nsd-control` write.

NSD will send notifications to secondary zones if a primary zone is updated. NSD
will check for updates at primary servers periodically and transfer the updated
zone by AXFR/IXFR and reload the new zone contents.

If you wish exert manual control use :command:`nsd-control notify`,
:command:`transfer` and :command:`force_transfer` commands.  The transfer
command will check for new versions of the secondary zones hosted by this NSD.
The notify command will send notifications to the secondary servers configured
in ``notify:`` statements.
