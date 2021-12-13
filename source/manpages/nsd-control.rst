.. _doc_nsd_control_manpage:

nsd-control(8)
--------------

.. raw:: html

    <pre class="man">nsd-control(8)                     nsd 4.3.9                    nsd-control(8)



    <b>NAME</b>
        <b>nsd-control,</b> <b>nsd-control-setup</b> - NSD remote server control utility.

    <b>SYNOPSIS</b>
        <b>nsd-control</b> [<b>-c</b> <i>cfgfile</i>] [<b>-s</b> <i>server</i>] <i>command</i>

    <b>DESCRIPTION</b>
        <b>nsd-control</b>  performs  remote  administration on the <a href="nsd.html"><i>nsd</i>(8)</a> DNS server.
        It reads the configuration file, contacts  the  nsd  server  over  SSL,
        sends the command and displays the result.

        The available options are:

        <b>-h</b>     Show the version and commandline option help.

        <b>-c</b> <i>cfgfile</i>
                The config file to read with settings.  If not given the default
                config file /etc/nsd/nsd.conf is used.

        <b>-s</b> <i>server[@port]</i>
                IPv4 or IPv6 address of the server to contact.   If  not  given,
                the address is read from the config file.

    <b>COMMANDS</b>
        There are several commands that the server understands.

        <b>start</b>  Start  the  server.  Simply execs <a href="nsd.html"><i>nsd</i>(8)</a>.  The nsd executable is
                searched for in the <b>PATH</b> set in the environment.  It is  started
                with  the  config  file specified using <i>-c</i> or the default config
                file.

        <b>stop</b>   Stop the server. The server daemon exits.

        <b>reload</b> <b>[&lt;zone&gt;]</b>
                Reload zonefiles and reopen  logfile.   Without  argument  reads
                changed  zonefiles.   With  argument  reads the zonefile for the
                given zone and loads it.

        <b>reconfig</b>
                Reload nsd.conf and apply changes to TSIG keys and configuration
                patterns, and apply the changes to add and remove zones that are
                mentioned in the config.  Other changes are not applied, such as
                listening  ip address and port and chroot, also per-zone statis-
                tics are not applied.  The pattern updates means that  the  con-
                figuration  options  for  zones  (request-xfr, zonefile, notify,
                ...) are updated.  Also new patterns are available for use  with
                the addzone command.

        <b>repattern</b>
                Same as the reconfig option.

        <b>log</b><i>_</i><b>reopen</b>
                Reopen  the  logfile, for log rotate that wants to move the log-
                file away and create a new logfile.  The log  can  also  be  re-
                opened with kill -HUP (which also reloads all zonefiles).

        <b>status</b> Display  server  status. Exit code 3 if not running (the connec-
                tion to the port is refused), 1 on error, 0 if running.

        <b>stats</b>  Output a sequence of name=value lines with  statistics  informa-
                tion, requires NSD to be compiled with this option enabled.

        <b>stats</b><i>_</i><b>noreset</b>
                Same as stats, but does not zero the counters.

        <b>addzone</b> <b>&lt;zone</b> <b>name&gt;</b> <b>&lt;pattern</b> <b>name&gt;</b>
                Add  a new zone to the running server.  The zone is added to the
                zonelist file on disk, so it stays after a restart.  The pattern
                name determines the options for the new zone.  For slave zones a
                zone transfer is immediately attempted.  For zones with a  zone-
                file, the zone file is attempted to be read in.

        <b>delzone</b> <b>&lt;zone</b> <b>name&gt;</b>
                Remove  the  zone  from the running server.  The zone is removed
                from the zonelist file on disk, from the nsd.db  file  and  from
                the memory.  If it had a zonefile, this remains (but may be out-
                dated).  Zones configured inside nsd.conf itself cannot  be  re-
                moved this way because the daemon does not write to the nsd.conf
                file, you need to add such zones to the zonelist file to be able
                to delete them with the delzone command.

        <b>changezone</b> <b>&lt;zone</b> <b>name&gt;</b> <b>&lt;pattern</b> <b>name&gt;</b>
                Change  a  zone  to  use  the  pattern for options.  The zone is
                deleted and added in one operation, changing it to use  the  new
                pattern for the zone options.  Zones configured in nsd.conf can-
                not be changed like this, instead edit the nsd.conf (or the  in-
                cluded file in nsd.conf) and reconfig.

        <b>addzones</b>
                Add  zones  read  from  stdin of nsd-control.  Input is read per
                line, with name space patternname on a  line.   For  bulk  addi-
                tions.

        <b>delzones</b>
                Remove  zones read from stdin of nsd-control.  Input is one name
                per line.  For bulk removals.

        <b>write</b> <b>[&lt;zone&gt;]</b>
                Write zonefiles to disk, or the given zonefile to  disk.   Zones
                that  have  changed  (via  AXFR  or IXFR) are written, or if the
                zonefile has not been created yet then it is created.  Directory
                components  of  the zonefile path are created if necessary. With
                argument that zone is written if it was modified, without  argu-
                ment, all modified zones are written.

        <b>notify</b> <b>[&lt;zone&gt;]</b>
                Send  NOTIFY  messages  to  slave  servers.  Sends to the IP ad-
                dresses configured in the 'notify:' lists for the  master  zones
                hosted  on this server.  Usually NSD sends NOTIFY messages right
                away when a master zone serial is updated.  If a zone is  given,
                notifies  are  sent for that zone.  These slave servers are sup-
                posed to initiate a zone transfer request later (to this  server
                or  another  master), this can be allowed via the 'provide-xfr:'
                acl list configuration. With argument that  zone  is  processed,
                without argument, all zones are processed.

        <b>transfer</b> <b>[&lt;zone&gt;]</b>
                Attempt  to update slave zones that are hosted on this server by
                contacting the masters.  The masters  are  configured  via  're-
                quest-xfr:'  lists.   If  a zone is given, that zone is updated.
                Usually NSD receives a NOTIFY from the masters  (configured  via
                'allow-notify:'  acl  list)  that  a  new  zone serial has to be
                transferred.  For zones with no content, NSD may have backed off
                from  asking often because the masters did not respond, but this
                command will reset the backoff to its initial timeout, for  fre-
                quent  retries.  With argument that zone is transferred, without
                argument, all zones are transferred.

        <b>force</b><i>_</i><b>transfer</b> <b>[&lt;zone&gt;]</b>
                Force update slave zones that are hosted on this  server.   Even
                if  the  master hosts the same serial number of the zone, a full
                AXFR is performed to fetch it.  If you  want  to  use  IXFR  and
                check  that the serial number increases, use the 'transfer' com-
                mand. With argument that zone is transferred, without  argument,
                all zones are transferred.

        <b>zonestatus</b> <b>[&lt;zone&gt;]</b>
                Print  state of the zone, the serial numbers and since when they
                have been acquired.  Also prints the  notify  action  (to  which
                server),  and  zone transfer (and from which master) if there is
                activity right now.  The state of the zone is printed as:  'mas-
                ter'  (master zones), 'ok' (slave zone is up-to-date), 'expired'
                (slave zone has expired), 'refreshing' (slave zone has transfers
                active).   The  serial  numbers  printed are the 'served-serial'
                (currently active), the 'commit-serial' (is in reload), the 'no-
                tified-serial' (got notify, busy fetching the data).  The serial
                numbers are only printed if such a serial number  is  available.
                With  argument that zone is printed, without argument, all zones
                are printed.

        <b>serverpid</b>
                Prints the PID of the server process.  This is used for  statis-
                tics  (and  only  works when NSD is compiled with statistics en-
                abled).  This pid is not for sending unix signals, use  the  pid
                from nsd.pid for that, that pid is also stable.

        <b>verbosity</b> <b>&lt;number&gt;</b>
                Change logging verbosity.

        <b>print</b><i>_</i><b>tsig</b> <b>[&lt;key</b><i>_</i><b>name&gt;]</b>
                print  the secret and algorithm for the TSIG key with that name.
                Or list all the tsig keys with their name, secret and algorithm.

        <b>update</b><i>_</i><b>tsig</b> <b>&lt;name&gt;</b> <b>&lt;secret&gt;</b>
                Change existing TSIG key with name to the new secret.   The  se-
                cret is a base64 encoded string.  The changes are only in-memory
                and are gone next restart, for lasting changes edit the nsd.conf
                file or a file included from it.

        <b>add</b><i>_</i><b>tsig</b> <b>&lt;name&gt;</b> <b>&lt;secret&gt;</b> <b>[algo]</b>
                Add  a  new  TSIG key with the given name, secret and algorithm.
                Without algorithm a default  (hmac-sha256)  algorithm  is  used.
                The secret is a base64 encoded string.  The changes are only in-
                memory and are gone next restart, for lasting changes  edit  the
                nsd.conf file or a file included from it.

        <b>assoc</b><i>_</i><b>tsig</b> <b>&lt;zone&gt;</b> <b>&lt;key</b><i>_</i><b>name&gt;</b>
                Associate  the  zone  with  the  given tsig.  The access control
                lists for notify, allow-notify, provide-xfr and request-xfr  are
                adjusted to use the given key.

        <b>del</b><i>_</i><b>tsig</b> <b>&lt;key</b><i>_</i><b>name&gt;</b>
                Delete  the  TSIG  key with the given name.  Prints error if the
                key is still in use by some zone.  The changes are only  in-mem-
                ory  and  are  gone  next  restart, for lasting changes edit the
                nsd.conf file or a file included from it.

        <b>add</b><i>_</i><b>cookie</b><i>_</i><b>secret</b> <b>&lt;secret&gt;</b>
                Add or replace a cookie secret persistently. &lt;secret&gt;  needs  to
                be an 128 bit hex string.

                Cookie  secrets  can  be either <i>active</i> or <i>staging</i>. <i>Active</i> cookie
                secrets are used to create DNS Cookies, but  verification  of  a
                DNS Cookie succeeds with any of the <i>active</i> or <i>staging</i> cookie se-
                crets. The state of the current cookie secrets  can  be  printed
                with the <b>print</b><i>_</i><b>cookie</b><i>_</i><b>secrets</b> command.

                When there are no cookie secrets configured yet, the &lt;secret&gt; is
                added as <i>active</i>. If there is already an  <i>active</i>  cookie  secret,
                the  &lt;secret&gt; is added as <i>staging</i> or replacing an existing <i>stag-</i>
                <i>ing</i> secret.

                To "roll" a cookie secret used in an anycast set. The new secret
                has  to  be  added as staging secret to <b>all</b> nodes in the anycast
                set. When <b>all</b> nodes can verify DNS Cookies with the new  secret,
                the  new secret can be activated with the <b>activate</b><i>_</i><b>cookie</b><i>_</i><b>secret</b>
                command. After <b>all</b> nodes have the new secret <i>active</i> for at least
                one   hour,   the  previous  secret  can  be  dropped  with  the
                <b>drop</b><i>_</i><b>cookie</b><i>_</i><b>secret</b> command.

                Persistence is accomplished by writing to a file which  if  con-
                figured with the <b>cookie-secret-file</b> option in the server section
                of  the  config  file.   The  default   value   for   that   is:
                /etc/nsd/nsd_cookiesecrets.txt .

        <b>drop</b><i>_</i><b>cookie</b><i>_</i><b>secret</b>
                Drop the <i>staging</i> cookie secret.

        <b>activate</b><i>_</i><b>cookie</b><i>_</i><b>secret</b>
                Make  the  current <i>staging</i> cookie secret <i>active</i>, and the current
                <i>active</i> cookie secret <i>staging</i>.

        <b>print</b><i>_</i><b>cookie</b><i>_</i><b>secrets</b>
                Show the current configured cookie secrets with their status.

    <b>EXIT</b> <b>CODE</b>
        The nsd-control program exits with status code 1 on error,  0  on  suc-
        cess.

    <b>SET</b> <b>UP</b>
        The  setup requires a self-signed certificate and private keys for both
        the server and client.  The script <i>nsd-control-setup</i> generates these in
        the  default  run  directory,  or with -d in another directory.  If you
        change the access control permissions on the key files you  can  decide
        who  can use nsd-control, by default owner and group but not all users.
        The script preserves private keys present in the directory.  After run-
        ning the script as root, turn on <b>control-enable</b> in <i>nsd.conf</i>.

    <b>STATISTIC</b> <b>COUNTERS</b>
        The <i>stats</i> command shows a number of statistic counters.

        <i>num.queries</i>
                number  of  queries received (the tls, tcp and udp queries added
                up).

        <i>serverX.queries</i>
                number of queries handled by the server process.  The number  of
                server processes is set with the config statement <b>server-count</b>.

        <i>time.boot</i>
                uptime in seconds since the server was started.  With fractional
                seconds.

        <i>time.elapsed</i>
                time since the last stats report, in seconds.   With  fractional
                seconds.   Can  be zero if polled quickly and the previous stats
                command resets the counters, so that the next gets a fully zero,
                and zero elapsed time, report.

        <i>size.db.disk</i>
                size of nsd.db on disk, in bytes.

        <i>size.db.mem</i>
                size of the DNS database in memory, in bytes.

        <i>size.xfrd.mem</i>
                size  of memory for zone transfers and notifies in xfrd process,
                excludes TSIG data, in bytes.

        <i>size.config.disk</i>
                size of zonelist file on disk, excludes the  nsd.conf  size,  in
                bytes.

        <i>size.config.mem</i>
                size  of  config  data  in memory, kept twice in server and xfrd
                process, in bytes.

        <i>num.type.X</i>
                number of queries with this query type.

        <i>num.opcode.X</i>
                number of queries with this opcode.

        <i>num.class.X</i>
                number of queries with this query class.

        <i>num.rcode.X</i>
                number of answers that carried this return code.

        <i>num.edns</i>
                number of queries with EDNS OPT.

        <i>num.ednserr</i>
                number of queries which failed EDNS parse.

        <i>num.udp</i>
                number of queries over UDP ip4.

        <i>num.udp6</i>
                number of queries over UDP ip6.

        <i>num.tcp</i>
                number of connections over TCP ip4.

        <i>num.tcp6</i>
                number of connections over TCP ip6.

        <i>num.tls</i>
                number of connections over TLS ip4.  TLS queries are not part of
                num.tcp.

        <i>num.tls6</i>
                number of connections over TLS ip6.  TLS queries are not part of
                num.tcp6.

        <i>num.answer_wo_aa</i>
                number of answers with NOERROR rcode and without AA  flag,  this
                includes the referrals.

        <i>num.rxerr</i>
                number of queries for which the receive failed.

        <i>num.txerr</i>
                number of answers for which the transmit failed.

        <i>num.raxfr</i>
                number  of  AXFR requests from clients (that got served with re-
                ply).

        <i>num.truncated</i>
                number of answers with TC flag set.

        <i>num.dropped</i>
                number of queries that were dropped because they  failed  sanity
                check.

        <i>zone.master</i>
                number  of  master  zones  served.  These are zones with no 're-
                quest-xfr:' entries.

        <i>zone.slave</i>
                number of  slave  zones  served.   These  are  zones  with  're-
                quest-xfr' entries.

    <b>FILES</b>
        <i>/etc/nsd/nsd.conf</i>
                nsd configuration file.

        <i>/etc/nsd</i>
                directory with private keys (nsd_server.key and nsd_control.key)
                and  self-signed  certificates  (nsd_server.pem   and   nsd_con-
                trol.pem).

    <b>SEE</b> <b>ALSO</b>
        <a href="nsd.conf.html"><i>nsd.conf</i>(5)</a>, <a href="nsd.html"><i>nsd</i>(8)</a>, <a href="nsd-checkconf.html"><i>nsd-checkconf</i>(8)</a>



    NLnet Labs                       Dec  9, 2021                   nsd-control(8)
    </pre>