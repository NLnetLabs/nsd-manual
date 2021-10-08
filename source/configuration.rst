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
