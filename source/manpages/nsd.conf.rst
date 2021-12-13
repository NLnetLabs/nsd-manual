.. _doc_nsd_conf_manpage:

nsd.conf(5)
-----------

.. raw:: html

    <pre class="man">nsd.conf(5)                        nsd 4.3.9                       nsd.conf(5)



    <b>NAME</b>
        <b>nsd.conf</b> - NSD configuration file

    <b>SYNOPSIS</b>
        <b>nsd.conf</b>

    <b>DESCRIPTION</b>
        <b>Nsd.conf</b>  is  used  to configure nsd(8). The file format has attributes
        and values. Some attributes have attributes inside them.  The  notation
        is: attribute: value.

        Comments  start with # and last to the end of line. Empty lines are ig-
        nored as is whitespace at the beginning of a line. Quotes can be  used,
        for names with spaces, eg. "file name.zone".

        <b>Nsd.conf</b>  specifies  options  for the nsd server, zone files, primaries
        and secondaries.

    <b>EXAMPLE</b>
        An example of a short nsd.conf file is below.

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

        Then, use kill -HUP to reload changes from master zone files.  And  use
        kill -TERM to stop the server.

    <b>FILE</b> <b>FORMAT</b>
        There  must be whitespace between keywords. Attribute keywords end with
        a colon ':'. An attribute is followed by its containing attributes,  or
        a value.

        At  the  top  level only <b>server:</b>, <b>key:</b>, <b>pattern:</b>, <b>zone:</b>, <b>tls-auth:</b>, and
        <b>remote-control:</b> are allowed. These are followed by their attributes  or
        a  new  top-level  keyword. The <b>zone:</b> attribute is followed by zone op-
        tions. The <b>server:</b> attribute is followed by global options for the  <b>NSD</b>
        server. A <b>key:</b> attribute is used to define keys for authentication. The
        <b>pattern:</b> attribute is followed by the zone options for zones  that  use
        the  pattern.   A <b>tls-auth:</b> attribute is used to define credentials for
        authenticating an outgoing TLS connection used for XFR-over-TLS.

        Files can be included using the <b>include:</b> directive. It can appear  any-
        where, and takes a single filename as an argument. Processing continues
        as if the text from the included file was copied into the  config  file
        at  that  point.   If  a  chroot is used an absolute filename is needed
        (with the chroot prepended), so that the include can be  parsed  before
        and after application of the chroot (and the knowledge of what that ch-
        root is).  You can use '*' to include a wildcard match  of  files,  eg.
        "foo/nsd.d/*.conf".   Also  '?', '{}', '[]', and '~' work, see <b>glob</b>(7).
        If no files match the pattern, this is not an error.

    <b>Server</b> <b>Options</b>
        The global options (if not overridden from  the  NSD  commandline)  are
        taken from the <b>server:</b> clause. There may only be one <b>server:</b> clause.

        <a id="ip-address"><b>ip-address:</b></a> &lt;ip4 or ip6&gt;[@port] [servers] [bindtodevice] [setfib]
                NSD  will  bind  to the listed ip-address. Can be given multiple
                times to bind multiple ip-addresses. Optionally, a  port  number
                can be given.  If none are given NSD listens to the wildcard in-
                terface. Same as commandline option <b>-a.</b>

                To limit which NSD server(s)  listen  on  the  given  interface,
                specify  one  or  more  servers  separated  by  whitespace after
                &lt;ip&gt;[@port]. Ranges can be used as a shorthand to specify multi-
                ple consecutive servers. By default every server will listen.

                If  an interface name is used instead of ip4 or ip6, the list of
                IP addresses associated with that interface  is  picked  up  and
                used at server start.

                For  servers with multiple IP addresses that can be used to send
                traffic to the internet, list them one by one, or the source ad-
                dress  of  replies  could  be wrong.  This is because if the udp
                socket associates a source address of 0.0.0.0  then  the  kernel
                picks  an  ip-address with which to send to the internet, and it
                picks the wrong one.  Typically needed  for  anycast  instances.
                Use  ip-transparent  to  be  able to list addresses that turn on
                later (typical for certain load-balancing).

        <a id="interface"><b>interface:</b></a> &lt;ip4 or ip6&gt;[@port] [servers] [bindtodevice] [setfib]
                Same  as  ip-address  (for  ease  of  compatibility   with   un-
                bound.conf).

        <a id="ip-transparent"><b>ip-transparent:</b></a> &lt;yes or no&gt;
                Allows  NSD  to  bind  to non local addresses. This is useful to
                have NSD listen to IP addresses that are not (yet) added to  the
                network  interface,  so  that it can answer immediately when the
                address is added. Default is no.

        <a id="ip-freebind"><b>ip-freebind:</b></a> &lt;yes or no&gt;
                Set the IP_FREEBIND option to bind to nonlocal addresses and in-
                terfaces  that are down.  Similar to ip-transparent.  Default is
                no.

        <a id="reuseport"><b>reuseport:</b></a> &lt;yes or no&gt;
                Use the SO_REUSEPORT socket option, and create file  descriptors
                for every server in the server-count.  This improves performance
                of the network stack.  Only really useful if you also  configure
                a  server-count  higher  than 1 (such as, equal to the number of
                cpus).  The default is no.  It works on Linux, but does not work
                on FreeBSD, and likely does not work on other systems.

        <a id="send-buffer-size"><b>send-buffer-size:</b></a> &lt;number&gt;
                Set  the send buffer size for query-servicing sockets.  Set to 0
                to use the default settings.

        <a id="receive-buffer-size"><b>receive-buffer-size:</b></a> &lt;number&gt;
                Set the receive buffer size for query-servicing sockets.  Set to
                0 to use the default settings.

        <a id="debug-mode"><b>debug-mode:</b></a> &lt;yes or no&gt;
                Turns on debugging mode for nsd, does not fork a daemon process.
                Default is no. Same as commandline option <b>-d.</b>  If set to yes  it
                does  not fork and stays in the foreground, which can be helpful
                for commandline debugging, but is also used  by  certain  server
                supervisor processes to ascertain that the server is running.

        <a id="do-ip4"><b>do-ip4:</b></a> &lt;yes or no&gt;
                If yes, NSD listens to IPv4 connections.  Default yes.

        <a id="do-ip6"><b>do-ip6:</b></a> &lt;yes or no&gt;
                If yes, NSD listens to IPv6 connections.  Default yes.

        <a id="database"><b>database:</b></a> &lt;filename&gt;
                By  default  <i>'/var/db/nsd/nsd.db'</i> is used. The specified file is
                used to store the compiled zone information. Same as commandline
                option  <b>-f.</b>   If  set to "" then no database is used.  This uses
                less memory but zone updates are not  (immediately)  spooled  to
                disk.

        <a id="zonelistfile"><b>zonelistfile:</b></a> &lt;filename&gt;
                By  default <i>/var/db/nsd/zone.list</i> is used. The specified file is
                used to store the dynamically added list of zones.  The list  is
                written  to  by  NSD to add and delete zones.  It is a text file
                with a zone-name and pattern-name on each line.   This  file  is
                used for the nsd-control addzone and delzone commands.

        <a id="identity"><b>identity:</b></a> &lt;string&gt;
                Returns  the specified identity when asked for CH TXT ID.SERVER.
                Default is the name as returned by gethostname(3). Same as  com-
                mandline  option <b>-i</b>.  See hide-identity to set the server to not
                respond to such queries.

        <a id="version"><b>version:</b></a> &lt;string&gt;
                Returns the specified version string when asked for CH TXT  ver-
                sion.server,  and version.bind queries.  Default is the compiled
                package version.  See hide-version to set the server to not  re-
                spond to such queries.

        <a id="nsid"><b>nsid:</b></a> &lt;string&gt;
                Add  the  specified  nsid to the EDNS section of the answer when
                queried with an NSID EDNS enabled packet.  As a sequence of  hex
                characters or with ascii_ prefix and then an ascii string.  Same
                as commandline option <b>-I</b>.

        <a id="logfile"><b>logfile:</b></a> &lt;filename&gt;
                Log messages to the logfile. The default is to log to stderr and
                syslog  (with  facility  LOG_DAEMON). Same as commandline option
                <b>-l</b>.

        <a id="log-only-syslog"><b>log-only-syslog:</b></a> &lt;yes or no&gt;
                Log messages only to syslog.  Useful with systemd so that  print
                to stderr does not cause duplicate log strings in journald.  Be-
                fore syslog has been opened, the server uses stderr.  Stderr  is
                also used if syslog is not available.  Default is no.

        <a id="server-count"><b>server-count:</b></a> &lt;number&gt;
                Start  this  many NSD servers. Default is 1. Same as commandline
                option <b>-N</b>.

        <a id="cpu-affinity"><b>cpu-affinity:</b></a> &lt;number&gt; &lt;number&gt; ...
                Overall CPU affinity for NSD server(s). Default is no  affinity.
                <b>-n</b>.

        <a id="server-N-cpu-affinity"><b>server-N-cpu-affinity:</b></a> &lt;number&gt;
                Bind NSD server specified by N to a specific core. Default is to
                have affinity set to every core specified in cpu-affinity.  This
                setting only takes effect if cpu-affinity is enabled.  <b>-n</b>

        <a id="xfrd-cpu-affinity"><b>xfrd-cpu-affinity:</b></a> &lt;number&gt;
                Bind xfrd to a specific core. Default is to have affinity set to
                every core specified in cpu-affinity. This  setting  only  takes
                effect if cpu-affinity is enabled.  <b>-n</b>

        <a id="tcp-count"><b>tcp-count:</b></a> &lt;number&gt;
                The maximum number of concurrent, active TCP connections by each
                server.  Default is 100. Same as commandline option <b>-n</b>.

        <a id="tcp-reject-overflow"><b>tcp-reject-overflow:</b></a> &lt;yes or no&gt;
                If set to yes, TCP connections made beyond the  maximum  set  by
                tcp-count  will  be  dropped  immediately (accepted and closed).
                Default is no.

        <a id="tcp-query-count"><b>tcp-query-count:</b></a> &lt;number&gt;
                The maximum number of queries served on a single TCP connection.
                Default is 0, meaning there is no maximum.

        <a id="tcp-timeout"><b>tcp-timeout:</b></a> &lt;number&gt;
                Overrides the default TCP timeout. This also affects zone trans-
                fers over TCP.  The default is 120 seconds.

        <a id="tcp-mss"><b>tcp-mss:</b></a> &lt;number&gt;
                Maximum segment size (MSS) of TCP socket on which the server re-
                sponds to queries. Value lower than common MSS on Ethernet (1220
                for example) will address path MTU problem.  Note that  not  all
                platform  supports  socket  option to set MSS (TCP_MAXSEG).  De-
                fault is system default MSS determined by interface MTU and  ne-
                gotiation between server and client.

        <a id="outgoing-tcp-mss"><b>outgoing-tcp-mss:</b></a> &lt;number&gt;
                Maximum  segment  size  (MSS) of TCP socket for outgoing XFR re-
                quest to other namesevers. Value lower than common MSS on Ether-
                net (1220 for example) will address path MTU problem.  Note that
                not all platform supports socket option to set MSS (TCP_MAXSEG).
                Default  is  system  default MSS determined by interface MTU and
                negotiation between NSD and other servers.

        <a id="ipv4-edns-size"><b>ipv4-edns-size:</b></a> &lt;number&gt;
                Preferred EDNS buffer size for IPv4.  Default 1232.

        <a id="ipv6-edns-size"><b>ipv6-edns-size:</b></a> &lt;number&gt;
                Preferred EDNS buffer size for IPv6.  Default 1232.

        <a id="pidfile"><b>pidfile:</b></a> &lt;filename&gt;
                Use the pid file instead of the platform specific default,  usu-
                ally  <i>/var/run/nsd.pid.</i>  Same as commandline option <b>-P</b>.  With ""
                there is no pidfile, for some startup management setups, where a
                pidfile is not useful to have.

        <a id="port"><b>port:</b></a> &lt;number&gt;
                Answer  queries  on  the  specified port. Default is 53. Same as
                commandline option <b>-p</b>.

        <a id="statistics"><b>statistics:</b></a> &lt;number&gt;
                If not present no statistics are dumped. Statistics are produced
                every number seconds. Same as commandline option <b>-s</b>.

        <a id="chroot"><b>chroot:</b></a> &lt;directory&gt;
                NSD will chroot on startup to the specified directory. Note that
                if elsewhere in the configuration you specify an absolute  path-
                name to a file inside the chroot, you have to prepend the <b>chroot</b>
                path. That way, you can switch the  chroot  option  on  and  off
                without having to modify anything else in the configuration. Set
                the value to "" (the empty string) to disable the chroot. By de-
                fault "" is used. Same as commandline option <b>-t</b>.

        <a id="username"><b>username:</b></a> &lt;username&gt;
                After  binding  the  socket, drop user privileges and assume the
                username. Can be username, id or id.gid. Same as commandline op-
                tion <b>-u</b>.

        <a id="zonesdir"><b>zonesdir:</b></a> &lt;directory&gt;
                Change  the  working directory to the specified directory before
                accessing zone files. Also, NSD will access <b>database</b>,  <b>zonelist-</b>
                <b>file</b>,   <b>logfile</b>,  <b>pidfile</b>,  <b>xfrdfile</b>,  <b>xfrdir</b>,  <b>server-key-file</b>,
                <b>server-cert-file</b>, <b>control-key-file</b> and  <b>control-cert-file</b>  rela-
                tive  to  this directory. Set the value to "" (the empty string)
                to  disable  the  change  of  working  directory.   By   default
                "<i>/etc/nsd</i>" is used.

        <a id="difffile"><b>difffile:</b></a> &lt;filename&gt;
                Ignored, for compatibility with NSD3 config files.

        <a id="xfrdfile"><b>xfrdfile:</b></a> &lt;filename&gt;
                The  soa  timeout  and zone transfer daemon in NSD will save its
                state to this file. State is read  back  after  a  restart.  The
                state  file can be deleted without too much harm, but timestamps
                of zones will be gone.  If it is configured  as  "",  the  state
                file  is  not used, all slave zones are checked for updates upon
                startup.  For more details see the section on zone expiry behav-
                ior of NSD. Default is <i>/var/db/nsd/xfrd.state</i>.

        <a id="xfrdir"><b>xfrdir:</b></a> &lt;directory&gt;
                The zone transfers are stored here before they are processed.  A
                directory is created here that is removed when NSD  exits.   De-
                fault is <i>/tmp</i>.

        <a id="xfrd-reload-timeout"><b>xfrd-reload-timeout:</b></a> &lt;number&gt;
                If this value is -1, xfrd will not trigger a reload after a zone
                transfer. If positive xfrd will trigger a reload  after  a  zone
                transfer,  then it will wait for the number of seconds before it
                will trigger a new reload.  Setting  this  value  throttles  the
                reloads to once per the number of seconds. The default is 1 sec-
                ond.

        <a id="verbosity"><b>verbosity:</b></a> &lt;level&gt;
                This value specifies the verbosity level  for  (non-debug)  log-
                ging.  Default is 0. 1 gives more information about incoming no-
                tifies and zone transfers. 2 lists soft warnings  that  are  en-
                countered. 3 prints more information.

                Verbosity  0  will  print  warnings and errors, and other events
                that are important to keep NSD running.

                Verbosity 1 prints additionally messages of interest.   Success-
                ful notifies, successful incoming zone transfer (the zone is up-
                dated), failed incoming  zone  transfers  or  the  inability  to
                process zone updates.

                Verbosity 2 prints additionally soft errors, like connection re-
                sets over TCP.  And notify refusal, and axfr request refusals.

        <a id="hide-version"><b>hide-version:</b></a> &lt;yes or no&gt;
                Prevent NSD from replying with the version string on CHAOS class
                queries.  Default is no.

        <a id="hide-identity"><b>hide-identity:</b></a> &lt;yes or no&gt;
                Prevent  NSD  from  replying  with  the identity string on CHAOS
                class queries.  Default is no.

        <a id="drop-updates"><b>drop-updates:</b></a> &lt;yes or no&gt;
                If set to yes, drop received packets  with  the  UPDATE  opcode.
                Default is no.

        <a id="use-systemd"><b>use-systemd:</b></a> &lt;yes or no&gt;
                This option is deprecated and ignored.  If compiled with libsys-
                temd, NSD signals readiness to systemd and use of the option  is
                not necessary.

        <a id="log-time-ascii"><b>log-time-ascii:</b></a> &lt;yes or no&gt;
                Log  time  in  ascii, if "no" then in seconds epoch.  Default is
                yes.  This chooses the format when logging to file.  The  print-
                out via syslog has a timestamp formatted by syslog.

        <a id="round-robin"><b>round-robin:</b></a> &lt;yes or no&gt;
                Enable  round  robin  rotation  of  records in the answer.  This
                changes the order of records in the answer and this may  balance
                load across them.  The default is no.

        <a id="minimal-responses"><b>minimal-responses:</b></a> &lt;yes or no&gt;
                Enable  minimal responses for smaller answers.  This makes pack-
                ets smaller.  Extra data is only added for referrals, when it is
                really  necessary.  This is different from the --enable-minimal-
                responses configure time option, that reduces packets,  but  ex-
                actly  to  the fragmentation length, the nsd.conf option reduces
                packets as small as possible.  The default is no.

        <a id="confine-to-zone"><b>confine-to-zone:</b></a> &lt;yes or no&gt;
                If set to yes, additional information will not be added  to  the
                response if the apex zone of the additional information does not
                match the apex zone of the initial  query  (E.G.  CNAME  resolu-
                tion). Default is no.

        <a id="refuse-any"><b>refuse-any:</b></a> &lt;yes or no&gt;
                Refuse queries of type ANY.  This is useful to stop query floods
                trying to get large responses.  Note that rrl ratelimiting  also
                has type ANY as a ratelimiting type.  It sends truncation in re-
                sponse to UDP type ANY queries,  and  it  allows  TCP  type  ANY
                queries like normal.  The default is no.

        <a id="zonefiles-check"><b>zonefiles-check:</b></a> &lt;yes or no&gt;
                Make  NSD check the mtime of zone files on start and sighup.  If
                you disable it it starts faster (less disk activity in case of a
                lot of zones).  The default is yes.  The nsd-control reload com-
                mand reloads zone files regardless of this option.

        <a id="zonefiles-write"><b>zonefiles-write:</b></a> &lt;seconds&gt;
                Write changed secondary zones to their zonefile every N seconds.
                If  the  zone (pattern) configuration has "" zonefile, it is not
                written.  Zones that have received  zone  transfer  updates  are
                written  to  their zonefile.  Default is 0 (disabled) when there
                is a database, and 3600 (1 hour) when database is "".  The data-
                base  also commits zone transfer contents.  You can configure it
                away from the default by putting the config statement for  zone-
                files-write: after the database: statement in the config file.

        <a id="rrl-size"><b>rrl-size:</b></a> &lt;numbuckets&gt;
                This  option  gives  the size of the hashtable. Default 1000000.
                More buckets use more memory, and reduce the chance of hash col-
                lisions.

        <a id="rrl-ratelimit"><b>rrl-ratelimit:</b></a> &lt;qps&gt;
                The max qps allowed (from one query source). Default is on (with
                a suggested 200 qps). If set to 0 then it is disabled (unlimited
                rate),  also  set  the whitelist-ratelimit to 0 to disable rate-
                limit processing.  If you set verbosity to 2 the blocked and un-
                blocked  subnets  are  logged.   Blocked queries are blocked and
                some receive TCP fallback  replies.   Once  the  rate  limit  is
                reached,  NSD  begins  dropping responses. However, one in every
                "rrl-slip" number of responses is allowed, with the TC bit  set.
                If  slip is set to 2, the outgoing response rate will be halved.
                If it's set to 3, the outgoing response rate will be  one-third,
                and  so  on.   If  you set rrl-slip to 10, traffic is reduced to
                1/10th.    Ratelimit   options   rrl-ratelimit,   rrl-size   and
                rrl-whitelist-ratelimit are updated when nsd-control reconfig is
                done (also the zone-specific ratelimit options are updated).

        <a id="rrl-slip"><b>rrl-slip:</b></a> &lt;numpackets&gt;
                This option controls the number of packets discarded  before  we
                send  back  a SLIP response (a response with "truncated" bit set
                to one). 0 disables the sending of SLIP packets, 1  means  every
                query  will  get a SLIP response.  Default is 2, cuts traffic in
                half and legit users have a fair chance to get a +TC response.

        <a id="rrl-ipv4-prefix-length"><b>rrl-ipv4-prefix-length:</b></a> &lt;subnet&gt;
                IPv4 prefix length. Addresses are grouped by netblock.   Default
                24.

        <a id="rrl-ipv6-prefix-length"><b>rrl-ipv6-prefix-length:</b></a> &lt;subnet&gt;
                IPv6  prefix length. Addresses are grouped by netblock.  Default
                64.

        <a id="rrl-whitelist-ratelimit"><b>rrl-whitelist-ratelimit:</b></a> &lt;qps&gt;
                The max qps for query  sorts  for  a  source,  which  have  been
                whitelisted.  Default  on  (with a suggested 2000 qps). With the
                rrl-whitelist option you can set  specific  queries  to  receive
                this  qps  limit  instead of the normal limit.  With the value 0
                the rate is unlimited.

        <a id="answer-cookie"><b>answer-cookie:</b></a> &lt;yes or no&gt;
                Enable to answer to requests containig DNS Cookies as  specified
                in RFC7873.  Default is no.

        <a id="cookie-secret"><b>cookie-secret:</b></a> &lt;128 bit hex string&gt;
                Servers  in  an  anycast  deployment  need to be able to  verify
                each other's DNS Server Cookies.  For  this they need  to  share
                the  secret  used  to construct and verify the DNS Cookies.  De-
                fault is a 128 bits random secret  generated  at  startup  time.
                This  option  is ignored if a <b>cookie-secret-file</b> is present.  In
                that case the secrets from that file are used in DNS Cookie cal-
                culations.

        <a id="cookie-secret-file"><b>cookie-secret-file:</b></a> &lt;filename&gt;
                File from which the secrets are read used in DNS Cookie calcula-
                tions. When this file exists, the secrets in this file are  used
                and the secret specified by the <b>cookie-secret</b> option is ignored.
                Default is /etc/nsd/nsd_cookiesecrets.txt

                The  content  of  this  file  must  be  manipulated   with   the
                <b>add</b><i>_</i><b>cookie</b><i>_</i><b>secret</b>, <b>drop</b><i>_</i><b>cookie</b><i>_</i><b>secret</b> and <b>activate</b><i>_</i><b>cookie</b><i>_</i><b>secret</b>
                commands to the <a href="nsd-control.html"><i>nsd-control</i>(8)</a> tool. Please see that manpage how
                to perform a safe cookie secret rollover.

        <a id="tls-service-key"><b>tls-service-key:</b></a> &lt;filename&gt;
                If  enabled, the server provides TLS service on TCP sockets with
                the TLS service port number.  The port number (853)  is  config-
                ured  with tls-port.  To turn it on, create an interface: option
                line in config with @port appended to the IP-address.  This cre-
                ates  the extra socket on which the DNS over TLS service is pro-
                vided.

                The file is the private key for the TLS session. The public cer-
                tificate  is  in the tls-service-pem file. Default is "", turned
                off. Requires a restart (a reload is not enough) if changed, be-
                cause  the  private  key is read while root permissions are held
                and before chroot (if any).

        <a id="tls-service-pem"><b>tls-service-pem:</b></a> &lt;filename&gt;
                The public key certificate pem file for the tls service. Default
                is "", turned off.

        <a id="tls-service-ocsp"><b>tls-service-ocsp:</b></a> &lt;filename&gt;
                The  ocsp  pem file for the tls service, for OCSP stapling.  De-
                fault is "", turned off.  An external process prepares  and  up-
                dates the OCSP stapling data.  Like this,
                    openssl ocsp -no_nonce \
                    -respout /path/to/ocsp.pem \
                    -CAfile /path/to/ca_and_any_intermediate.pem \
                    -issuer /path/to/direct_issuer.pem \
                    -cert /path/to/cert.pem \
                    -url  "$( openssl x509 -noout -text -in /path/to/cert.pem |
                    grep 'OCSP - URI:' | cut -d: -f2,3 )"

        <a id="tls-port"><b>tls-port:</b></a> &lt;number&gt;
                The port number on which to provide TCP TLS service, default  is
                853, only interfaces configured with that port number as @number
                get DNS over TLS service.

        <a id="tls-cert-bundle"><b>tls-cert-bundle:</b></a> &lt;filename&gt;
                If null or "", the default verify locations are used. Set it  to
                the certificate bundle file, for example "/etc/pki/tls/certs/ca-
                bundle.crt". These  certificates  are  used  for  authenticating
                Transfer over TLS (XoT) connections.

    <b>Remote</b> <b>Control</b>
        The  <b>remote-control:</b>  clause  is  used  to  set  options  for using the
        <a href="nsd-control.html"><i>nsd-control</i>(8)</a> tool to give commands to the running NSD server.  It  is
        disabled by default, and listens for localhost by default.  It uses TLS
        over TCP where the server and client authenticate to  each  other  with
        self-signed  certificates.   The self-signed certificates can be gener-
        ated with the <i>nsd-control-setup</i> tool.  The key files are  read  by  NSD
        before  the chroot and before dropping user permissions, so they can be
        outside the chroot and readable by the superuser only.

        <a id="control-enable"><b>control-enable:</b></a> &lt;yes or no&gt;
                Enable remote control, default is no.

        <a id="control-interface"><b>control-interface:</b></a> &lt;ip4 or ip6 | interface name | absolute path&gt;
                NSD will bind to the listed addresses  to  service  control  re-
                quests  (on  TCP).  Can be given multiple times to bind multiple
                ip-addresses.  Use 0.0.0.0 and ::0 to service the  wildcard  in-
                terface.   If  none  are  given  NSD  listens  to  the localhost
                127.0.0.1 and ::1 interfaces for control, if control is  enabled
                with control-enable.

                If  an interface name is used instead of ip4 or ip6, the list of
                IP addresses associated with that interface  is  picked  up  and
                used at server start.

                With  an absolute path, a unix local named pipe is used for con-
                trol.  The file is created with user and group that  is  config-
                ured  and  access bits are set to allow members of the group ac-
                cess.  Further access can be controlled by  setting  permissions
                on  the  directory  containing the control socket file.  The key
                and cert files are not used when control is via the named  pipe,
                because access control is via file and directory permission.

        <a id="control-port"><b>control-port:</b></a> &lt;number&gt;
                The port number for remote control service. 8952 by default.

        <a id="server-key-file"><b>server-key-file:</b></a> &lt;filename&gt;
                Path     to    the    server    private    key,    by    default
                <i>/etc/nsd/nsd_server.key</i>.  This file is generated by the <i>nsd-con-</i>
                <i>trol-setup</i>  utility.   This  file is used by the nsd server, but
                not by <i>nsd-control</i>.

        <a id="server-cert-file"><b>server-cert-file:</b></a> &lt;filename&gt;
                Path  to  the  server  self  signed  certificate,   by   default
                <i>/etc/nsd/nsd_server.pem</i>.  This file is generated by the <i>nsd-con-</i>
                <i>trol-setup</i> utility.  This file is used by the  nsd  server,  and
                also by <i>nsd-control</i>.

        <a id="control-key-file"><b>control-key-file:</b></a> &lt;filename&gt;
                Path   to   the   control   client   private   key,  by  default
                <i>/etc/nsd/nsd_control.key</i>.   This  file  is  generated   by   the
                <i>nsd-control-setup</i> utility.  This file is used by <i>nsd-control</i>.

        <a id="control-cert-file"><b>control-cert-file:</b></a> &lt;filename&gt;
                Path   to   the   control   client   certificate,   by   default
                <i>/etc/nsd/nsd_control.pem</i>.  This certificate  has  to  be  signed
                with  the  server  certificate.   This  file is generated by the
                <i>nsd-control-setup</i> utility.  This file is used by <i>nsd-control</i>.

    <b>Pattern</b> <b>Options</b>
        The <b>pattern:</b> clause is used to denote a set of options to apply to some
        zones.  The same zone options as for a zone are allowed.

        <a id="name"><b>name:</b></a> &lt;string&gt;
                The  name  of  the  pattern.  This is a (case sensitive) string.
                The pattern names that start with "_implicit_" are  used  inter-
                nally  for  zones  that  have  no  pattern  (they are defined in
                nsd.conf directly).

        <a id="include-pattern"><b>include-pattern:</b></a> &lt;pattern-name&gt;
                The options from the given pattern are included at this point in
                this pattern.  The referenced pattern must be defined above this
                one.

        <a id="<zone</b> <b>option>"><b>&lt;zone</b> <b>option&gt;:</b></a> &lt;value&gt;
                The zone options such as  <b>zonefile</b>,  <b>allow-query</b>,  <b>allow-notify</b>,
                <b>request-xfr</b>,  <b>allow-axfr-fallback</b>,  <b>notify</b>,  <b>notify-retry</b>,  <b>pro-</b>
                <b>vide-xfr</b>, <b>zonestats</b>, and <b>outgoing-interface</b> can be given.   They
                are applied to the patterns and zones that include this pattern.

    <b>Zone</b> <b>Options</b>
        For  every  zone  the options need to be specified in one <b>zone:</b> clause.
        The access control list elements can be given  multiple  times  to  add
        multiple servers. These elements need to be added explicitly.

        For  zones  that  are configured in the <i>nsd.conf</i> config file their set-
        tings are hardcoded (in an implicit pattern for  themselves  only)  and
        they  cannot  be  deleted  via delzone, but remove them from the config
        file and repattern.

        <a id="name"><b>name:</b></a> &lt;string&gt;
                The name of the zone. This is the domain name of the apex of the
                zone.  May end with a '.' (in FQDN notation). For example "exam-
                ple.com", "sub.example.net.". This attribute must be present  in
                each zone.

        <a id="zonefile"><b>zonefile:</b></a> &lt;filename&gt;
                The  file  containing the zone information. If this attribute is
                present it is used to read and write the zone contents.  If  the
                attribute is absent it prevents writing out of the zone.

                The  string  is  processed  so that one string can be used (in a
                pattern) for a lot of different zones.  If the label or  charac-
                ter  does not exist the percent-character is replaced with a pe-
                riod for output (i.e. for the third character in  a  two  letter
                domain name).

                <b>%s</b> is replaced with the zone name.

                <b>%1</b> is replaced with the first character of the zone name.

                <b>%2</b> is replaced with the second character of the zone name.

                <b>%3</b> is replaced with the third character of the zone name.

                <b>%z</b> is replaced with the toplevel domain name of the zone.

                <b>%y</b> is replaced with the next label under the toplevel domain.

                <b>%x</b>  is  replaced with the next-next label under the toplevel do-
                main.

        <a id="allow-query"><b>allow-query:</b></a> &lt;ip-spec&gt; &lt;key-name | NOKEY | BLOCKED&gt;
                Access control list.  When at least one  <b>allow-query</b>  option  is
                specified,  then  the  in  the <b>allow-query</b> options specified ad-
                dresses are are allowed  to  query  the  server  for  the  zone.
                Queries from unlisted or specifically BLOCKED addresses are dis-
                carded. If  NOKEY  is  given  no  TSIG  signature  is  required.
                BLOCKED  supersedes other entries, other entries are scanned for
                a match in the order of the statements.  Without <b>allow-query</b> op-
                tions,  queries are allowed from any IP address without TSIG key
                (which is the default).

                The ip-spec is either a plain IP address (IPv4 or IPv6), or  can
                be   a   subnet   of   the   form  1.2.3.4/24,  or  masked  like
                1.2.3.4&amp;255.255.255.0 or a range of the  form  1.2.3.4-1.2.3.25.
                Note the ip-spec ranges do not use spaces around the /, &amp;, @ and
                - symbols.

        <a id="allow-notify"><b>allow-notify:</b></a> &lt;ip-spec&gt; &lt;key-name | NOKEY | BLOCKED&gt;
                Access control list. The listed (primary) address is allowed  to
                send notifies to this (secondary) server. Notifies from unlisted
                or specifically BLOCKED addresses are  discarded.  If  NOKEY  is
                given  no  TSIG signature is required.  BLOCKED supersedes other
                entries, other entries are scanned for a match in the  order  of
                the statements.

                The  ip-spec is either a plain IP address (IPv4 or IPv6), or can
                be  a  subnet  of  the   form   1.2.3.4/24,   or   masked   like
                1.2.3.4&amp;255.255.255.0  or  a range of the form 1.2.3.4-1.2.3.25.
                A port number can be added using a suffix of @number, for  exam-
                ple  1.2.3.4@5300  or  1.2.3.4/24@5300  for port 5300.  Note the
                ip-spec ranges do not use spaces around the /, &amp;, @ and  -  sym-
                bols.

        <b>request-xfr:</b> [AXFR|UDP] &lt;ip-address&gt; &lt;key-name | NOKEY&gt; [tls-auth-name]
                Access  control list. The listed address (the master) is queried
                for AXFR/IXFR on update. A port number can be added using a suf-
                fix  of  @number, for example 1.2.3.4@5300. The specified key is
                used during AXFR/IXFR. If tls-auth-name is included, the  speci-
                fied  tls-auth clause will be used to perform authenticated XFR-
                over-TLS.

                If the AXFR option is given, the server will  not  be  contacted
                with  IXFR  queries  but  only AXFR requests will be made to the
                server. This allows an NSD secondary to  have  a  master  server
                that runs NSD. If the AXFR option is left out then both IXFR and
                AXFR requests are made to the master server.

                If the UDP option is given, the secondary will use UDP to trans-
                mit  the IXFR requests. You should deploy TSIG when allowing UDP
                transport, to authenticate notifies and zone  transfers.  Other-
                wise,  NSD is more vulnerable for Kaminsky-style attacks. If the
                UDP option is left out then IXFR will be transmitted using TCP.

                If a tls-auth-name is given then TLS (by default  on  port  853)
                will be used for all zone transfers for the zone. If authentica-
                tion of the master based on the specified  tls-auth  authentica-
                tion  information  fails, the XFR request will not be sent. Sup-
                port for TLS 1.3 is required for XFR-over-TLS.

        <a id="allow-axfr-fallback"><b>allow-axfr-fallback:</b></a> &lt;yes or no&gt;
                This option should be accompanied by request-xfr. It (dis)allows
                NSD  (as  secondary)  to  fallback  to  AXFR if the primary name
                server does not support IXFR. Default is yes.

        <a id="size-limit-xfr"><b>size-limit-xfr:</b></a> &lt;number&gt;
                This option should be accompanied by request-xfr.  It  specifies
                XFR  temporary  file  size  limit.   It can be used to stop very
                large zone retrieval, that could otherwise use up a lot of  mem-
                ory  and  disk  space.   If this option is 0, unlimited. Default
                value is 0.

        <a id="notify"><b>notify:</b></a> &lt;ip-address&gt; &lt;key-name | NOKEY&gt;
                Access control list. The listed address (a secondary)  is  noti-
                fied of updates to this zone. A port number can be added using a
                suffix of @number, for example 1.2.3.4@5300. The  specified  key
                is  used  to  sign  the notify. Only on secondary configurations
                will NSD be able to detect zone updates (as it gets notified it-
                self, or refreshes after a time).

        <a id="notify-retry"><b>notify-retry:</b></a> &lt;number&gt;
                This  option should be accompanied by notify. It sets the number
                of retries when sending notifies.

        <a id="provide-xfr"><b>provide-xfr:</b></a> &lt;ip-spec&gt; &lt;key-name | NOKEY | BLOCKED&gt;
                Access control list. The listed address (a secondary) is allowed
                to  request AXFR from this server. Zone data will be provided to
                the address. The specified key is used during AXFR. For unlisted
                or  BLOCKED  addresses  no  data  is provided, requests are dis-
                carded.  BLOCKED supersedes other  entries,  other  entries  are
                scanned  for  a  match in the order of the statements.  NSD pro-
                vides AXFR for its secondaries,  but  IXFR  is  not  implemented
                (IXFR is implemented for request-xfr, but not for provide-xfr).

                The  ip-spec is either a plain IP address (IPv4 or IPv6), or can
                be  a  subnet  of  the   form   1.2.3.4/24,   or   masked   like
                1.2.3.4&amp;255.255.255.0  or  a range of the form 1.2.3.4-1.2.3.25.
                A port number can be added using a suffix of @number, for  exam-
                ple  1.2.3.4@5300  or  1.2.3.4/24@5300  for  port 5300. Note the
                ip-spec ranges do not use spaces around the /, &amp;, @ and  -  sym-
                bols.

        <a id="outgoing-interface"><b>outgoing-interface:</b></a> &lt;ip-address&gt;
                Access  control  list.  The  listed  address  is used to request
                AXFR|IXFR (in case of a secondary) or used to send notifies  (in
                case of a primary).

                The  ip-address  is  a  plain IP address (IPv4 or IPv6).  A port
                number can be added using  a  suffix  of  @number,  for  example
                1.2.3.4@5300.

        <a id="max-refresh-time"><b>max-refresh-time:</b></a> &lt;seconds&gt;
                Limit refresh time for secondary zones.  This is the timer which
                checks to see if the zone has to be refetched when  it  expires.
                Normally  the value from the SOA record is used, but this option
                restricts that value.

        <a id="min-refresh-time"><b>min-refresh-time:</b></a> &lt;seconds&gt;
                Limit refresh time for secondary zones.

        <a id="max-retry-time"><b>max-retry-time:</b></a> &lt;seconds&gt;
                Limit retry time for secondary zones.  This is the  timer  which
                retries after a failed fetch attempt for the zone.  Normally the
                value from the SOA record is used, followed  by  an  exponential
                backoff, but this option restricts that value.

        <a id="min-retry-time"><b>min-retry-time:</b></a> &lt;seconds&gt;
                Limit retry time for secondary zones.

        <a id="min-expire-time"><b>min-expire-time:</b></a> &lt;seconds or refresh+retry+1&gt;
                Limit  expire  time  for  secondary zones.  The value can be ex-
                pressed either by a  number  of  seconds,  or  the  string  "re-
                fresh+retry+1".   With  the latter the expire time will be lower
                bound to the refresh plus the retry value from the  SOA  record,
                plus  1.   The  refresh  and retry values will be subject to the
                bounds  configured  with   max-refresh-time,   min-refresh-time,
                max-retry-time and min-retry-time if given.

        <a id="zonestats"><b>zonestats:</b></a> &lt;name&gt;
                When  compiled  with --enable-zone-stats NSD can collect statis-
                tics per zone.  This name gives the group where  statistics  are
                added  to.   The  groups  are  output from nsd-control stats and
                stats_noreset.  Default is "".  You can use "%s" to use the name
                of  the  zone  to track its statistics.  If not compiled in, the
                option can be given but is ignored.

        <a id="include-pattern"><b>include-pattern:</b></a> &lt;pattern-name&gt;
                The options from the given pattern are included at  this  point.
                The referenced pattern must be defined above this zone.

        <a id="rrl-whitelist"><b>rrl-whitelist:</b></a> &lt;rrltype&gt;
                This  option  causes  queries of this rrltype to be whitelisted,
                for this zone. They receive  the  whitelist-ratelimit.  You  can
                give   multiple   lines,  each  enables  a  new  rrltype  to  be
                whitelisted for the zone. Default has none whitelisted. The rrl-
                type  is  the  query  classification that the NSD RRL employs to
                make different types not interfere with one another.  The  types
                are  logged  in  the  loglines when a subnet is blocked (in ver-
                bosity 2).  The RRL classification types are:  nxdomain,  error,
                referral, any, rrsig, wildcard, nodata, dnskey, positive, all.

        <a id="multi-master-check"><b>multi-master-check:</b></a> &lt;yes or no&gt;
                Default  no.   If  enabled, checks all masters for the last ver-
                sion.  It uses the higher version of all the configured masters.
                Useful  if you have multiple masters that have different version
                numbers served.

    <b>Key</b> <b>Declarations</b>
        The <b>key:</b> clause establishes a key for use in access control  lists.  It
        has the following attributes.

        <a id="name"><b>name:</b></a> &lt;string&gt;
                The  key  name.  Used to refer to this key in the access control
                list.  The key name has to be correct for tsig to work.  This is
                because the key name is output on the wire.

        <a id="algorithm"><b>algorithm:</b></a> &lt;string&gt;
                Authentication  algorithm  for  this  key.   Such  as  hmac-md5,
                hmac-sha1,    hmac-sha224,    hmac-sha256,    hmac-sha384    and
                hmac-sha512.   Can also be abbreviated as 'sha1', 'sha256'.  De-
                fault is sha256.  Algorithms are only available when  they  were
                compiled in (available in the crypto library).

        <a id="secret"><b>secret:</b></a> &lt;base64 blob&gt;
                The  base64 encoded shared secret. It is possible to put the <b>se-</b>
                <b>cret:</b> declaration (and base64 blob) into a different  file,  and
                then  to  <b>include:</b> that file. In this way the key secret and the
                rest of the configuration file, which may have  different  secu-
                rity policies, can be split apart.  The content of the secret is
                the agreed base64 secret content.  To make it up, enter a  pass-
                word (its length must be a multiple of 4 characters, A-Za-z0-9),
                or use dev-random output through a base64 encode filter.

    <b>TLS</b> <b>Auth</b> <b>Declarations</b>
        The <b>tls-auth:</b> clause establishes authentication attributes to use  when
        authenticating the far end of an outgoing TLS connection used in access
        control lists for XFR-over-TLS.  It has the following attributes.

        <a id="name"><b>name:</b></a> &lt;string&gt;
                The tls-auth name. Used to refer to this TLS authentication  in-
                formation in the access control list.

        <a id="auth-domain-name"><b>auth-domain-name:</b></a> &lt;string&gt;
                The authentication domain name as defined in RFC8310.

        <b>client-cert:</b> <b>&lt;file</b> <b>name</b> <b>of</b> <b>clientcert.pem&gt;</b>
                If  you want to use mutual TLS authentication, this is where the
                client certificates can be configured that NSD uses  to  connect
                to  the  upstream server to download the zone. The client public
                key pem cert file can be configured here. Also configure a  pri-
                vate key with client-key.

        <b>client-key:</b> <b>&lt;file</b> <b>name</b> <b>of</b> <b>clientkey.key&gt;</b>
                If  you  want  to use mutual TLS authentication, the private key
                file can be configured here for the client authentication.

        <b>client-key-pw:</b> <b>&lt;string&gt;</b>
                If the client-key file uses a password to decrypt the key before
                it  can  be  used,  then the password can be specified here as a
                string.  It is possible to include other config files  with  the
                include:  option,  and  this  can be used to move that sensitive
                data to another file, if you wish.

    <b>DNSTAP</b> <b>Logging</b> <b>Options</b>
        DNSTAP support, when compiled in, is enabled in  the  <b>dnstap:</b>  section.
        This  starts a collector process that writes the log information to the
        destination.

        <a id="dnstap-enable"><b>dnstap-enable:</b></a> &lt;yes or no&gt;
                If dnstap is enabled.  Default no.  If yes, it connects  to  the
                dnstap  server  and if any of the dnstap-log-..-messages options
                is enabled it sends logs for those messages to the server.

        <a id="dnstap-socket-path"><b>dnstap-socket-path:</b></a> &lt;file name&gt;
                Sets the unix socket file name for connecting to the server that
                is   listening   on  that  socket.   Default  is  "/var/run/nsd-
                dnstap.sock".

        <a id="dnstap-send-identity"><b>dnstap-send-identity:</b></a> &lt;yes or no&gt;
                If enabled, the server identity is included in the log messages.
                Default is no.

        <a id="dnstap-send-version"><b>dnstap-send-version:</b></a> &lt;yes or no&gt;
                If  enabled, the server version if included in the log messages.
                Default is no.

        <a id="dnstap-identity"><b>dnstap-identity:</b></a> &lt;string&gt;
                The identity to send with messages, if "" the hostname is  used.
                Default is "".

        <a id="dnstap-version"><b>dnstap-version:</b></a> &lt;string&gt;
                The  version to send with messages, if "" the package version is
                used.  Default is "".

        <a id="dnstap-log-auth-query-messages"><b>dnstap-log-auth-query-messages:</b></a> &lt;yes or no&gt;
                Enable to log auth query messages.  Default is  no.   These  are
                client queries to NSD.

        <a id="dnstap-log-auth-response-messages"><b>dnstap-log-auth-response-messages:</b></a> &lt;yes or no&gt;
                Enable to log auth response messages.  Default is no.  These are
                responses from NSD to clients.

    <b>NSD</b> <b>CONFIGURATION</b> <b>FOR</b> <b>BIND9</b> <b>HACKERS</b>
        BIND9 is a name server implementation with its own  configuration  file
        format, named.conf(5). BIND9 types zones as 'Master' or 'Slave'.

    <b>Slave</b> <b>zones</b>
        For a slave zone, the master servers are listed. The master servers are
        queried for zone data, and are listened to  for  update  notifications.
        In  NSD these two properties need to be configured separately, by list-
        ing the master address in allow-notify and request-xfr statements.

        In BIND9 you only need to provide allow-notify elements for  any  extra
        sources  of  notifications  (i.e. the operators), NSD needs to have al-
        low-notify for both masters  and  operators.  BIND9  allows  additional
        transfer sources, in NSD you list those as request-xfr.

        Here is an example of a slave zone in BIND9 syntax.

        # Config file for example.org options {
                dnssec-enable yes;
        };

        key tsig.example.org. {
                algorithm hmac-md5;
                secret "aaaaaabbbbbbccccccdddddd";
        };

        server 162.0.4.49 {
                keys { tsig.example.org. ; };
        };

        zone "example.org" {
                type slave;
                file "secondary/example.org.signed";
                masters { 162.0.4.49; };
        };

        For NSD, DNSSEC is enabled automatically for zones that are signed. The
        dnssec-enable statement in the options clause is  not  needed.  In  NSD
        keys  are  associated  with  an  IP  address in the access control list
        statement, therefore the server{} statement is not needed. Below is the
        same example in an NSD config file.

        # Config file for example.org
        key:
                name: tsig.example.org.
                algorithm: hmac-md5
                secret: "aaaaaabbbbbbccccccdddddd"

        zone:
                name: "example.org"
                zonefile: "secondary/example.org.signed"
                # the master is allowed to notify and will provide zone data.
                allow-notify: 162.0.4.49 NOKEY
                request-xfr: 162.0.4.49 tsig.example.org.

        Notice  that the master is listed twice, once to allow it to send noti-
        fies to this slave server and once to tell the slave  server  where  to
        look for updates zone data. More allow-notify and request-xfr lines can
        be added to specify more masters.

        It is possible to specify extra allow-notify lines for  addresses  that
        are also allowed to send notifications to this slave server.

    <b>Master</b> <b>zones</b>
        For  a  master zone in BIND9, the slave servers are listed. These slave
        servers are sent notifications of updated and are  allowed  to  request
        transfer  of the zone data. In NSD these two properties need to be con-
        figured separately.

        Here is an example of a master zone in BIND9 syntax.

        zone "example.nl" {
                type master;
                file "example.nl";
        };

        In NSD syntax this becomes:

        zone:
                name: "example.nl"
                zonefile: "example.nl"
                # allow anybody to request xfr.
                provide-xfr: 0.0.0.0/0 NOKEY
                provide-xfr: ::0/0 NOKEY

                # to list a slave server you would in general give
                # provide-xfr: 1.2.3.4 tsig-key.name.
                # notify: 1.2.3.4 NOKEY

    <b>Other</b>
        NSD is an authoritative only DNS server. This means that it is meant as
        a  primary or secondary server for zones, providing DNS data to DNS re-
        solvers and caches. BIND9 can function as an authoritative DNS  server,
        the  configuration  options for that are compared with those for NSD in
        this section. However, BIND9 can also function as a resolver or  cache.
        The  configuration  options  that BIND9 has for the resolver or caching
        thus have no equivalents for NSD.

    <b>FILES</b>
        "/var/db/nsd/nsd.db"
                default <b>NSD</b> database

        /etc/nsd/nsd.conf
                default <b>NSD</b> configuration file

    <b>SEE</b> <b>ALSO</b>
        <a href="nsd.html"><i>nsd</i>(8)</a>, <a href="nsd-checkconf.html"><i>nsd-checkconf</i>(8)</a>, <a href="nsd-control.html"><i>nsd-control</i>(8)</a>

    <b>AUTHORS</b>
        <b>NSD</b> was written by NLnet Labs and RIPE NCC joint team. Please see CRED-
        ITS file in the distribution for further details.

    <b>BUGS</b>
        <b>nsd.conf</b>  is parsed by a primitive parser, error messages may not be to
        the point.



    NLnet Labs                       Dec  9, 2021                      nsd.conf(5)
    </pre>