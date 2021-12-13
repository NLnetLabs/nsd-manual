.. _doc_nsd_checkzone_manpage:

nsd-checkzone(8)
----------------

.. raw:: html

    <pre class="man">nsd-checkzone(8)                   nsd 4.3.9                  nsd-checkzone(8)



    <b>NAME</b>
        <b>nsd-checkzone</b> - NSD zone file syntax checker.

    <b>SYNOPSIS</b>
        <b>nsd-checkzone</b> [<b>-h</b>] <i>zonename</i> <i>zonefile</i>

    <b>DESCRIPTION</b>
        <b>nsd-checkzone</b>  reads  a  DNS  zone  file  and checks it for errors.  It
        prints errors to stderr.  On failure it exits with nonzero exit status.

        This is used to check files before feeding them to the nsd(8) daemon.

    <b>OPTIONS</b>
        <b>-h</b>     Print usage help information and exit.

        <i>zonename</i>
                The name of the zone to check, eg. "example.com".

        <i>zonefile</i>
                The file to read, eg. "zones/example.com.zone.signed".

    <b>SEE</b> <b>ALSO</b>
        <a href="nsd.html"><i>nsd</i>(8)</a>, <a href="nsd-checkconf.html"><i>nsd-checkconf</i>(8)</a>

    <b>AUTHORS</b>
        <b>NSD</b> was written by NLnet Labs and RIPE NCC joint team. Please see CRED-
        ITS file in the distribution for further details.



    NLnet Labs                       Dec  9, 2021                 nsd-checkzone(8)
    </pre>