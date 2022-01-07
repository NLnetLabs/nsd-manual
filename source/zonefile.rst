.. _doc_nsd_zonefile:

Zonefile example
----------------

On this page we give an example of a basic zone file and it's contents.

We recommend using the :command:`nsd-checkzone` tool to verify that you have a working zone.

Creating a zone
===============

A zone needs a SOA (Source Of Authority) record. For the exact structure we refer you to `the wiki page <https://en.wikipedia.org/wiki/SOA_record>`_. Note that all records must Fully Qualified Domain Names (FQDNs) which adds a ``.`` to the domain name. In this example the FQDN is: ``example.com.``. This is in contrast to relative domain names, where the origin gets appended (so in the example below, ``www`` gets expanded to ``www.example.com.``). 

Also note that ``@`` symbol in the zone file refers to the ``$ORIGIN`` parameter. 
To have multi-line resource records opening and closing brackets can be used to ignore linebreaks. Finally, if a name at the start of a record is missed, the name from the previous entry gets used (This is why all three A records are equivalent).

.. code:: bash
		
	$ORIGIN example.com. ; 'default' domain as FQDN for this zone
	$TTL 86400 ; default time-to-live for this zone

	example.com.   IN  SOA     ns.example.com. noc.dns.icann.org. (
	        2020080302  ;Serial
	        7200        ;Refresh
	        3600        ;Retry
	        1209600     ;Expire
	        3600        ;Negative response caching TTL
	)

	; The nameserver that are authoritative for this zone.
					NS	example.com.

	; these A records below are equivalent
	example.com.	A	192.0.2.1
	@				A	192.0.2.1
					A	192.0.2.1

	@				AAAA 2001:db8::3

	; A CNAME redirect from www.exmaple.com to example.com
	www				CNAME   example.com.

	mail			MX	10	example.com.

	1.2.0.192.in-addr.arpa". PTR example.com.



.. could add this structure eventually: <name> <ttl> <class> <type> <rdata>













