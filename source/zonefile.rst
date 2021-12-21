Zonefile example
================

On this page we give an example of a basic zone file and it's contents.

Creating a zone
---------------

A zone needs a SOA (Source Of Authority) record. ``WHY?``  For the exact
structure we refer you to the wiki page ``LINK
https://en.wikipedia.org/wiki/SOA_record``.

..code::bash

	$ORIGIN example.com. ; 'default' domain as FQDN for this zone
	$TTL 86400 ; default time-to-live for this zone

	zone.example.com.   IN  SOA     ns.example.com. noc.dns.icann.org. (
	        2020080302  ;Serial
	        7200        ;Refresh
	        3600        ;Retry
	        1209600     ;Expire
	        3600        ;Negative response caching TTL
	)

	; The nameserver that are authoritative for this zone. These need to be FQDNs
					NS	example.com.

	; these are equivalent A records
	example.com.	A	192.0.1.1
	@				A	192.0.1.1
					A	192.0.1.1

	www				@	192.0.2.1

	mail			MX	10	example.com.







.. explain @ -> The "@" symbol in this example indicates that this is a record for the root domain


.. explain the need for FQDN and the substitution that happens otherwise ie. example.com.example.com.















