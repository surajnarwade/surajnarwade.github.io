+++
title = "Types of DNS records"
slug = "types-of-dns-records"
description = "Learn the main types of dns records and when each one is used."
author = "Suraj Narwade"
date = "2023-08-23T05:17:15.741Z"
category = "Blog"
+++

While working with [route53](https://aws.amazon.com/route53/), I came across a couple of DNS records which were new to me.


There are several types of DNS (Domain Name System) records, each with a specific purpose. Here are some of the most common types:


### A record (Address record)


Maps a hostname to an IPv4 address.



```
example.com IN A 192.0.2.1

```

### AAAA record (IPv6 Address record)


Maps a hostname to an IPv6 address.



```
example.com IN AAAA 2001:db8::1

```

### CNAME record (Canonical Name record)


Maps an alias hostname to the true hostname (canonical name).



```
www.example.com IN CNAME example.com

```

### MX record (Mail Exchange record)


Specifies the mail servers that should be used to handle email for a particular domain.



```
 example.com IN MX 10 mailserver.example.com

```

### NS record (Name Server record)


Specifies the authoritative DNS servers for a particular domain.



```
example.com IN NS ns1.example.com

```

### PTR record (Pointer record)


Maps an IP address to a hostname (reverse DNS lookup).



```
1.2.3.4.in-addr.arpa IN PTR example.com

```

### SOA record (Start of Authority record)


Specifies a domain's primary authoritative DNS server and provides information about the zone.



```
example.com IN SOA ns1.example.com admin.example.com 2019032101 7200 3600 1209600 3600

```

### TXT record (Text record)


Stores arbitrary text data associated with a domain.



```
example.com IN TXT "v=spf1 mx -all"

```

### SRV records


it is also known as Service records. it is useful for services that operate on non\-standard ports or for load balancing between multiple servers providing the same service. it is used to define the hostname, port number, priority, and weight of a server that provides a particular service for a domain.



```
_Service._Proto.Name TTL Class Priority Weight Port Target
_imaps._tcp.example.com. 3600 IN SRV 10 5 993 mailserver.example.com.

```

Hope this was helpful, Happy Hacking :)


