.. _doc_nsd_checkconf_manpage:

nsd-checkconf(8)
----------------

.. raw:: html

    <pre class="man">nsd-checkconf(8)                   nsd 4.3.9                  nsd-checkconf(8)



    <b>NAME</b>
        <b>nsd-checkconf</b> - NSD configuration file checker.

    <b>SYNOPSIS</b>
        <b>nsd-checkconf</b> [<b>-v</b>] [<b>-f</b>] [<b>-h</b>] [<b>-o</b> <i>option</i>] [<b>-z</b> <i>zonename</i>] [<b>-p</b> <i>pattern</i>] [<b>-s</b>
        <i>keyname</i>] [<b>-t</b> <i>tlsauthname</i>] <i>configfile</i>

    <b>DESCRIPTION</b>
        <b>nsd-checkconf</b> reads a configuration file. It  prints  parse  errors  to
        standard  error,  and  performs  additional checks on the contents. The
        configfile format is described in nsd.conf(5).

        The utility of this program is to check a config file for errors before
        using  it in nsd(8). This program can also be used for shell scripts to
        access the nsd config file, using the -o and -z options.

    <b>OPTIONS</b>
        <b>-v</b>     After reading print the options to standard output in configfile
                format.  Without  this  option, only success or parse errors are
                reported.

        <b>-f</b>     Print full pathname when used with files, like with -o  pidfile.
                This  includes  the  chroot in the way it is applied to the pid-
                file.

        <b>-h</b>     Print usage help information and exit.

        <b>-o</b> <i>option</i>
                Return only this option from the config file. This option can be
                used  in  conjunction  with the <b>-z</b> and the <b>-p</b> option, or without
                them to query the server:  section.   The  special  value  <i>zones</i>
                prints  out  a list of configured zones.  The special value <i>pat-</i>
                <i>terns</i> prints out a list of configured patterns.

                This option can be used to parse the config file from the shell.
                If the <b>-z</b> option is given, but the <b>-o</b> option is not given, noth-
                ing is printed.

        <b>-s</b> <i>keyname</i>
                Prints the key secret (base64 blob) configured for this  key  in
                the  config  file.  Used  to help shell scripts parse the config
                file.

        <b>-t</b> <i>tls-auth</i>
                Prints the authentication domain name configured for  this  tls-
                auth clause in the config file. Used to help shell scripts parse
                the config file.

        <b>-p</b> <i>pattern</i>
                Return the option specified with <b>-o</b> for the given pattern name.

        <b>-z</b> <i>zonename</i>
                Return the option specified with <b>-o</b> for zone 'zonename'.

                If this option is not given, the server section  of  the  config
                file is used.

                The  -o,  -s  and -z option print configfile options to standard
                output.

    <b>FILES</b>
        /etc/nsd/nsd.conf
                default <b>NSD</b> configuration file

    <b>SEE</b> <b>ALSO</b>
        <a href="/documentation/nsd/nsd/"><i>nsd</i>(8)</a>, <a href="/documentation/nsd/nsd.conf/"><i>nsd.conf</i>(5)</a>, <a href="/documentation/nsd/nsd-control/"><i>nsd-control</i>(8)</a>

    <b>AUTHORS</b>
        <b>NSD</b> was written by NLnet Labs and RIPE NCC joint team. Please see CRED-
        ITS file in the distribution for further details.



    NLnet Labs                       Dec  9, 2021                 nsd-checkconf(8)
    </pre>