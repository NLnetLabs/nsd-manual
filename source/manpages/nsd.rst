.. _doc_nsd_manpage:

nsd(8)
------

.. raw:: html

    <pre class="man">NSD(8)                             NSD 4.3.9                            NSD(8)



   <b>NAME</b>
         <b>nsd</b> - Name Server Daemon (NSD) version 4.3.9.

   <b>SYNOPSIS</b>
         <b>nsd</b> [<b>-4</b>] [<b>-6</b>] [<b>-a</b> <i>ip-address[@port]</i>] [<b>-c</b> <i>configfile</i>] [<b>-d</b>] [<b>-f</b> <i>database</i>]
         [<b>-h</b>] [<b>-i</b> <i>identity</i>] [<b>-I</b> <i>nsid</i>] [<b>-l</b> <i>logfile</i>] [<b>-N</b> <i>server-count</i>] [<b>-n</b> <i>noncur-</i>
         <i>rent-tcp-count</i>]  [<b>-P</b> <i>pidfile</i>] [<b>-p</b> <i>port</i>] [<b>-s</b> <i>seconds</i>] [<b>-t</b> <i>chrootdir</i>] [<b>-u</b>
         <i>username</i>] [<b>-V</b> <i>level</i>] [<b>-v</b>]

   <b>DESCRIPTION</b>
         <b>NSD</b> is a complete implementation of an  authoritative  DNS  nameserver.
         Upon startup, <b>NSD</b> will read the database specified with <b>-f</b> <i>database</i> ar-
         gument and put itself into background and answers queries on port 53 or
         a different port specified with <b>-p</b> <i>port</i> option. The <i>database</i> is created
         if it does not exist. By default, <b>NSD</b> will bind to all local interfaces
         available. Use the <b>-a</b> <i>ip-address[@port]</i> option to specify a single par-
         ticular interface address to be bound. If this  option  is  given  more
         than  once,  <b>NSD</b> will bind its UDP and TCP sockets to all the specified
         ip-addresses separately. If IPv6 is enabled when  <b>NSD</b>  is  compiled  an
         IPv6 address can also be specified.

   <b>OPTIONS</b>
         All  the options can be specified in the configfile ( <b>-c</b> argument), ex-
         cept for the <b>-v</b> and <b>-h</b> options. If options are specified on the comman-
         dline,  the options on the commandline take precedence over the options
         in the configfile.

         Normally <b>NSD</b> should be started with the `nsd-control(8) start`  command
         invoked from a <i>/etc/rc.d/nsd.sh</i> script or similar at the operating sys-
         tem startup.

         <b>-4</b>     Only listen to IPv4 connections.

         <b>-6</b>     Only listen to IPv6 connections.

         <b>-a</b> <i>ip-address[@port]</i>
               Listen to the specified  <i>ip-address</i>.   The  <i>ip-address</i>  must  be
               specified in numeric format (using the standard IPv4 or IPv6 no-
               tation). Optionally, a port number can be given.  This flag  can
               be  specified multiple times to listen to multiple IP addresses.
               If this flag is not specified, <b>NSD</b> listens to the  wildcard  in-
               terface.

         <b>-c</b> <i>configfile</i>
               Read    specified    <i>configfile</i>    instead    of   the   default
               <i>/etc/nsd/nsd.conf</i>.  For format description see nsd.conf(5).

         <b>-d</b>     Do not fork, stay in the foreground.

         <b>-f</b> <i>database</i>
               Use  the  specified  <i>database</i>  instead   of   the   default   of
               <i>'/var/db/nsd/nsd.db'</i>.  If a <b>zonesdir:</b> is specified in the config
               file this path can be relative to that directory.

         <b>-h</b>     Print help information and exit.

         <b>-i</b> <i>identity</i>
               Return the specified <i>identity</i> when asked for  <i>CH</i>  <i>TXT</i>  <i>ID.SERVER</i>
               (This  option is used to determine which server is answering the
               queries when they are anycast). The default is the name returned
               by gethostname(3).

         <b>-I</b> <i>nsid</i>
               Add  the  specified  <i>nsid</i> to the EDNS section of the answer when
               queried with an NSID EDNS enabled packet.  As a sequence of  hex
               characters or with ascii_ prefix and then an ascii string.

         <b>-l</b> <i>logfile</i>
               Log messages to the specified <i>logfile</i>.  The default is to log to
               stderr and syslog. If a <b>zonesdir:</b> is  specified  in  the  config
               file this path can be relative to that directory.

         <b>-N</b> <i>count</i>
               Start  <i>count</i> <b>NSD</b> servers. The default is 1. Starting more than a
               single server is only useful  on  machines  with  multiple  CPUs
               and/or network adapters.

         <b>-n</b> <i>number</i>
               The maximum <i>number</i> of concurrent TCP connection that can be han-
               dled by each server. The default is 100.

         <b>-P</b> <i>pidfile</i>
               Use the specified <i>pidfile</i> instead of the platform  specific  de-
               fault,  which  is  mostly  <i>/var/run/nsd.pid</i>.   If a <b>zonesdir:</b> is
               specified in the config file, this path can be relative to  that
               directory.

         <b>-p</b> <i>port</i>
               Answer the queries on the specified <i>port</i>.  Normally this is port
               53.

         <b>-s</b> <i>seconds</i>
               Produce statistics dump every <i>seconds</i> seconds. This is equal  to
               sending <i>SIGUSR1</i> to the daemon periodically.

         <b>-t</b> <i>chroot</i>
               Specifies a directory to <i>chroot</i> to upon startup. This option re-
               quires you to ensure that appropriate  syslogd(8)  socket  (e.g.
               <i>chrootdir</i>  /dev/log)  is  available, otherwise <b>NSD</b> won't produce
               any log output.

         <b>-u</b> <i>username</i>
               Drop user and group privileges to those of <i>username</i> after  bind-
               ing  the  socket.  The <i>username</i> must be one of: username, id, or
               id.gid. For example: nsd, 80, or 80.80.

         <b>-V</b> <i>level</i>
               This value specifies the verbosity level  for  (non-debug)  log-
               ging.  Default is 0.

         <b>-v</b>     Print the version number of <b>NSD</b> to standard error and exit.

         <b>NSD</b> reacts to the following signals:

         SIGTERM
               Stop answering queries, shutdown, and exit normally.

         SIGHUP Reload.   Scans zone files and if changed (mtime) reads them in.
               Also reopens the logfile (assists logrotation).

         SIGUSR1
               Dump BIND8-style statistics into the log. Ignored otherwise.

   <b>FILES</b>
         "/var/db/nsd/nsd.db"
               default <b>NSD</b> database

         /var/run/nsd.pid
               the process id of the name server.

         /etc/nsd/nsd.conf
               default <b>NSD</b> configuration file

   <b>DIAGNOSTICS</b>
         <b>NSD</b> will log all the problems via the standard syslog(8) <i>daemon</i>  facil-
         ity, unless the <b>-d</b> option is specified.

   <b>SEE</b> <b>ALSO</b>
         <a href="nsd.conf.html"><i>nsd.conf</i>(5)</a>, <a href="nsd-checkconf.html"><i>nsd-checkconf</i>(8)</a>, <a href="nsd-control.html"><i>nsd-control</i>(8)</a>

   <b>AUTHORS</b>
         <b>NSD</b> was written by NLnet Labs and RIPE NCC joint team. Please see CRED-
         ITS file in the distribution for further details.



   NLnet Labs                       Dec  9, 2021                           NSD(8)
   </pre>