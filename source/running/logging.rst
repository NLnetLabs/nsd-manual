.. _doc_nsd_logging:

Logging
-------

NSD doesn't do any logging. We believe that logging is a separate task and has
to be done independently from the core operation. This consciously is not part
of NSD itself in order to keep NSD focused and minimise its complexity. It is
better to leave logging and tracing to separate dedicated tools. 

The `CAIDA dnsstat tool <https://www.caida.org/catalog/software/dnsstat/>`_ can
easily be configured and/or modified to suit local statistics requirements
without any danger of affecting the name server itself. We have run ``dnsstat``
on the same machine as NSD, we would recommend using a multiprocessor if
performance is an issue. Of course it can also run on a separate machine that
has MAC layer access to the network of the server.

The ``nsd-control`` tool can output some statistics, with ``nsd-control stats``
and ``nsd-control stats_noreset``.  In `contrib/nsd_munin_
<https://github.com/NLnetLabs/nsd/blob/master/contrib/nsd_munin_>`_ there is a
Munin grapher plugin that uses it.  The output of ``nsd-control stats`` is easy
to read (text only) with scripts.  The output values are documented on the
``nsd-control`` man page.

Another available tool is `dnstop
<http://dns.measurement-factory.com/tools/dnstop/>`_, that displays DNS
statistics on your network.