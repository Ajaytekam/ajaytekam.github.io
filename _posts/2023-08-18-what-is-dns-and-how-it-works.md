---   
title: What is DNS and how it works
author: ajaytekam   
date: 2023-08-18 11:30:00 +0530   
img_path: /assets/posts/20230818/ 
categories: [DNS]
tags: [DNS, Networking]
image:
  path: dns0.jpg  
---    

## Contents 

- [How DNS Works](#how-dns-works)
- [Types of DNS Servers](#types-of-dns-servers)
- [Types of DNS Queries](#types-of-dns-queries)
    - [Recursive Query](#recursive-query)
    - [Iterative Query](#iterative-query)
    - [Non-Recursive Query](#non-recursive-query)
- [Resource Records (RR) and Zone file](#resource-records-rr-and-zone-file)
- [DNS Types/Record Types](#dns-typesrecord-types)
- [Tools to perform DNS query nslookup](#tools-to-perform-dns-query-nslookup)
    - [nslookup](#nslookup)
    - [DNS Lookup with dig](#dns-lookup-with-dig)
- [Reverse DNS Lookup](#reverse-dns-lookup)
- [DNS Zone Transfer](#dns-zone-transfer)
    - [Initiating ZONE Transfer](#initiating-zone-transfer)

## How DNS Works

DNS is a global system for translating IP addresses to human-readable domain names. When a user tries to access a web address like “example.com”, their web browser or application performs a DNS Query against a DNS server, supplying the hostname. The DNS server takes the hostname and resolves it into a numeric IP address, which the web browser can connect to.

A component called a DNS Resolver is responsible for checking if the hostname is available in local cache, and if not, contacts a series of DNS Name Servers, until eventually it receives the IP of the service the user is trying to reach, and returns it to the browser or application. This usually takes less than a second.

## Types of DNS Servers

The following are the most common DNS server types that are used to resolve hostnames into IP addresses.

* **1. DNS Resolver :** A DNS resolver (recursive resolver), is designed to receive DNS queries, which include a human-readable hostname such as “www.example.com”, and is responsible for tracking the IP address for that hostname.

* **2.DNS Root Server :** The root server is the first step in the journey from hostname to IP address. The DNS Root Server extracts the Top Level Domain (TLD) from the user’s query — for example, www.example.com — and provides details for the .com TLD Name Server. In turn, that server will provide details for domains with the .com DNS zone, including “example.com”. There are 12 root servers worldwide, indicated by the letters A through M, operated by organizations like the Internet Systems Consortium, Verisign, ICANN, the University of Maryland, and the U.S. Army Research Lab.

* **3. Authoritative DNS Server:** Higher level servers in the DNS hierarchy define which DNS server is the “authoritative” name server for a specific hostname, meaning that it holds the up-to-date information for that hostname. The Authoritative Name Server is the last stop in the name server query—it takes the hostname and returns the correct IP address to the DNS Resolver (or if it cannot find the domain, returns the message NXDOMAIN).

## Types of DNS Queries

### Recursive Query

In a recursive query, a DNS client provides a hostname, and the DNS Resolver “must” provide an answer—it responds with either a relevant resource record, or an error message if it can't be found. The resolver starts a recursive query process, starting from the DNS Root Server, until it finds the Authoritative Name Server that holds the IP address and other information for the requested hostname.

### Iterative Query

In an iterative query, a DNS client provides a hostname, and the DNS Resolver returns the best answer it can. If the DNS resolver has the relevant DNS records in its cache, it returns them. If not, it refers the DNS client to the Root Server, or another Authoritative Name Server which is nearest to the required DNS zone. The DNS client must then repeat the query directly against the DNS server it was referred to.

### Non-Recursive Query

A non-recursive query is a query in which the DNS Resolver already knows the answer. It either immediately returns a DNS record because it already stores it in local cache, or queries a DNS Name Server which is authoritative for the record, meaning it definitely holds the correct IP for that hostname. In both cases, there is no need for additional rounds of queries (like in recursive or iterative queries). Rather, a response is immediately returned to the client.

![](dns1.png)

(Image source : tcpipguide.com)

## Resource Records (RR) and Zone file

A zone file is a file on the server that contains entries for different Resource Records (RR). These records can provide us a bunch of information about the domain.

## DNS Types/Record Types

DNS resource records are contents of the DNS zone file. The zone file contains mappings between domain names and IP addresses in the form of text records. There are many types of the resource records.

* **1. Address Mapping record (A Record)** : also known as a DNS host record, stores a hostname and its corresponding IPv4 address.
* **2. IP Version 6 Address record (AAAA Record)** : stores a hostname and its corresponding IPv6 address.
* **3. Canonical Name record (CNAME Record)** : can be used to alias a hostname to another hostname. When a DNS client requests a record that contains a CNAME, which points to another hostname, the DNS resolution process is repeated with the new hostname.
* **4. Mail exchanger record (MX Record)** : specifies an SMTP email server for the domain, used to route outgoing emails to an email server.
* **5 Name Server records (NS Record)** : specifies that a DNS Zone, such as “example.com” is delegated to a specific Authoritative Name Server, and provides the address of the name server.
* **6. Reverse-lookup Pointer records (PTR Record)** : allows a DNS resolver to provide an IP address and receive a hostname (reverse DNS lookup).
* **SOA (Source Of Authority) Records** : this type of record holds information about the zone itself and about other records. Each zone will be having only one SOA record.

An example of a zone file is as follows :
```shell
1.  $ORIGIN example.com.     ; designates the start of this zone file in the namespace
2.  $TTL 1h                  ; default expiration time of all resource records without their own TTL value
3.  example.com.     IN  SOA   ns.example.com. username.example.com. (
4.                                                                2007120710 ; serial number of this zone file
5.                                                                1d ; refresh time for slave
6.                                                                2h ; retry time for slave
7.                                                                4w ; expiration time for slave
8.                                                                1h ; maximum caching time
9.                                                              )
10. example.com.     IN  NS    ns.example.com        ; ns.example.com is a nameserver for example.com
11. example.com.     IN  NS    ns.somewhere.example. ; ns.somewhere.example is a backup nameserver for example.com
12. example.com.     IN  MX    10 mail.example.com.  ; mail.example.com is the mailserver for example.com
13. @                IN  MX    20 mail2.example.com. ; equivalent to above line, "@" represents zone origin
14. @                IN  MX    50 mail3              ; equivalent to above line, but using a relative host name
15. example.com.     IN  A     192.0.2.1             ; IPv4 address for example.com
16.                  IN  AAAA  2001:db8:10::1        ; IPv6 address for example.com
17. ns.example.com   IN  A     192.0.2.2             ; IPv4 address for ns.example.com
18.                  IN  AAAA  2001:db8:10::2        ; IPv6 address for ns.example.com
19. www              IN  CNAME example.com.          ; www.example.com is an alias for example.com
20. wwwtest          IN  CNAME www                   ; wwwtest.example.com is another alias for www.example.com
21. mail             IN  A     192.0.2.3             ; IPv4 address for mail.example.com
22. mail2            IN  A     192.0.2.4             ; IPv4 address for mail2.example.com
23. mail3            IN  A     192.0.2.5             ; IPv4 address for mail3.example.com
```
(from https://en.wikipedia.org/wiki/Zone_file)

As we can that the domain records are written in format
```
//domain_name    record_class record_type   some_additional_information
// Example :

example.com.         IN          NS         ns.example.com
```

* The second line `TTL 1h` (time to live), it means that all the resource records have an expiration time of 1hr, after this time every record will have to make another query and refresh it’s data.
* From line 3 to 9 the SOA record is defined for ns.example.com and username.example.com.
* line 10 contains the authoritative name server ns.example.com for example.com.
* line 11 contains the backup name server ns.somewhere.com for example.com.
* line 12, 13 and 14 tells about the mail server which are responsible for receiving mails sent to the domain example.com.
* line 15 contains `A` record which basically maps example.com to 192.0.2.1
* line 16 contains `AAAA` record, which basically maps example.com to IPv6 version.
* line 17 is also `A` record for name server, which maps ns.example.com to 192.0.2.2, and line 18 is `AAAA` record is for IPv6.
* line 19 and 20 is for `CNAME` (canonical names) records or alias for example.com.
* line 21, 22, and 23 contains `A` record for mail servers.

## Tools to perform DNS query `nslookup`

### nslookup

Used for query Internet domain name servers.

* **Interactive mode :**

Starting it by :
```shell
$ nslookup
```
A simple example of sending `A` type query
```shell
$ nslookup
> google.com
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	google.com
Address: 172.217.163.142
Name:	google.com
Address: 2404:6800:4009:80e::200e
```
By default it sends `A` type query, and the DNS server currently serving our reqeust is 127.0.0.53 on port 53 (udp), thats why line 6 shows the `Non-authoritative answer`. Note that querying from your own dns server may not give you the accurate information every time. We can change the current DNS server by command

`server <dns_server_ip_address>`

```shell
> server 8.8.8.8
Default server: 8.8.8.8
Address: 8.8.8.8#53
> google.com
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
Name:	google.com
Address: 172.217.160.206
Name:	google.com
Address: 2404:6800:4009:807::200e
```
**Options available in nslookup :**

* set querytype :

(‘A/A’ servers returns host_name/ip_address of DNS server)

```shell
> set querytype=A
> google.com
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
Name:	google.com
Address: 216.58.199.174
```

* (‘NS/ns’ servers returns host_name/ip_address of name server)

```shell
> set querytype=NS
> google.com
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
google.com	nameserver = ns1.google.com.
google.com	nameserver = ns2.google.com.
google.com	nameserver = ns3.google.com.
google.com	nameserver = ns4.google.com.
```

The above nameservers are the authoritative name servers for google.com, now we can set our querying server to one of these name servers and then query for google.com

```shell
> server ns1.google.com
> set querytype=A
> google.com
Server:		ns1.google.com
Address:	216.239.32.10#53

Name:	google.com
Address: 216.58.203.174
```

This time it does not showed the message `Non-authoritative answer:`. And it shows similar for NS

```shell
> google.com
Server:		ns1.google.com
Address:	216.239.32.10#53

google.com	nameserver = ns3.google.com.
google.com	nameserver = ns2.google.com.
google.com	nameserver = ns4.google.com.
google.com	nameserver = ns1.google.com.
```

* (‘MX/mx’ servers returns host_name/ip_address of mail server)

```shell
> set querytype=MX
> google.com
Server:		ns1.google.com
Address:	216.239.32.10#53

google.com	mail exchanger = 10 aspmx.l.google.com.
google.com	mail exchanger = 30 alt2.aspmx.l.google.com.
google.com	mail exchanger = 40 alt3.aspmx.l.google.com.
google.com	mail exchanger = 20 alt1.aspmx.l.google.com.
google.com	mail exchanger = 50 alt4.aspmx.l.google.com.
```

* (`CNAME` returns the Canonical names of given servers address)

```shell
> set querytype=CNAME
> thehackernew.com
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
thehackernew.com	canonical name = HDRedirect-LB5-1afb6e2973825a56.elb.us-east-1.amazonaws.com.
```

**Other setting options :**

* retry : sets the retry option. (default is 3)
* timeout : sets the timeout option. (default is 0)
* port : change the zone transfer port. (53 is default)
* server : sets the root domain name.

Automating things with bash shell:
```shell
$ printf "google.com\nset querytype=ns\ngoogle.com" | nslookup
```

* **Non-Intercative mode :**

A simple query :
```shell
$ nslookup domain_name/ip_address
```
Example :
```shell
$ nslookup google.com
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	google.com
Address: 172.217.166.174
Name:	google.com
Address: 2404:6800:4007:80e::200e
```
Using different DNS/name server
```shell
$ nslookup domain_name/ip_address dns_server
```
Example :
```shell
$ nslookup google.com 8.8.8.8
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
Name:	google.com
Address: 216.58.199.174
Name:	google.com
Address: 2404:6800:4009:80b::200e


$ nslookup google.com ns1.google.com
Server:		ns1.google.com
Address:	216.239.32.10#53

Name:	google.com
Address: 216.58.203.174
Name:	google.com
Address: 2404:6800:4009:803::200e
```
Changing querytype
```shell
$ nslookup -type=A/NS/MX domain_name
```
Example :
```shell
$ nslookup -type=A google.com
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	google.com
Address: 172.217.163.142


$ nslookup -type=NS google.com
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
google.com	nameserver = ns2.google.com.
google.com	nameserver = ns4.google.com.
google.com	nameserver = ns1.google.com.
google.com	nameserver = ns3.google.com.


$ nslookup -type=MX google.com
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
google.com	mail exchanger = 30 alt2.aspmx.l.google.com.
google.com	mail exchanger = 20 alt1.aspmx.l.google.com.
google.com	mail exchanger = 40 alt3.aspmx.l.google.com.
google.com	mail exchanger = 50 alt4.aspmx.l.google.com.
google.com	mail exchanger = 10 aspmx.l.google.com.
```

### DNS Lookup with dig


`dig` performs DNS lookups and displays the answers that are returned from the name server(s) that were queried.

* Obtain the latest list of root domain servers (currently which you are using).

```shell
$ dig . ns

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> . ns
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 41313
;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;.				IN	NS

;; ANSWER SECTION:
.			3072	IN	NS	m.root-servers.net.
.			3072	IN	NS	l.root-servers.net.
.			3072	IN	NS	k.root-servers.net.
.			3072	IN	NS	j.root-servers.net.
.			3072	IN	NS	i.root-servers.net.
.			3072	IN	NS	h.root-servers.net.
.			3072	IN	NS	g.root-servers.net.
.			3072	IN	NS	f.root-servers.net.
.			3072	IN	NS	e.root-servers.net.
.			3072	IN	NS	d.root-servers.net.
.			3072	IN	NS	c.root-servers.net.
.			3072	IN	NS	b.root-servers.net.
.			3072	IN	NS	a.root-servers.net.

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Wed May 13 20:19:07 IST 2020
;; MSG SIZE  rcvd: 239
```

* Getting the dns-server details of a domain

```shell
$ dig google.com A

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> google.com A
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5165
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		112	IN	A	172.217.166.174

;; Query time: 33 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Wed May 13 20:19:57 IST 2020
;; MSG SIZE  rcvd: 55
```

* Getting the name-server details of a domain

```shell
$ dig google.com NS

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> google.com NS
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51912
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;google.com.			IN	NS

;; ANSWER SECTION:
google.com.		3045	IN	NS	ns2.google.com.
google.com.		3045	IN	NS	ns1.google.com.
google.com.		3045	IN	NS	ns3.google.com.
google.com.		3045	IN	NS	ns4.google.com.

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Wed May 13 20:20:42 IST 2020
;; MSG SIZE  rcvd: 111
```

* Using another name server for dns queries

```shell
$ dig @8.8.8.8 google.com A

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> @8.8.8.8 google.com A
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 35724
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		249	IN	A	216.58.196.78

;; Query time: 86 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Wed May 13 20:22:06 IST 2020
;; MSG SIZE  rcvd: 55
```

* Getting details of mail servers

```shell
$ dig @8.8.8.8 google.com MX

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> @8.8.8.8 google.com MX
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 28692
;; flags: qr rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.			IN	MX

;; ANSWER SECTION:
google.com.		373	IN	MX	50 alt4.aspmx.l.google.com.
google.com.		373	IN	MX	20 alt1.aspmx.l.google.com.
google.com.		373	IN	MX	10 aspmx.l.google.com.
google.com.		373	IN	MX	40 alt3.aspmx.l.google.com.
google.com.		373	IN	MX	30 alt2.aspmx.l.google.com.

;; Query time: 103 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Wed May 13 20:22:53 IST 2020
;; MSG SIZE  rcvd: 147
```

## Reverse DNS Lookup

Reverse DNS Lookups gets hostname for given IP address

```shell
$ dig -x ip_adderss
```

Example :

```shell
$ dig -x 157.240.16.35

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> -x 157.240.16.35
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 1894
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;35.16.240.157.in-addr.arpa.	IN	PTR

;; ANSWER SECTION:
35.16.240.157.in-addr.arpa. 3600 IN	PTR	edge-star-mini-shv-01-bom1.facebook.com.

;; Query time: 111 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Wed May 13 20:48:42 IST 2020
;; MSG SIZE  rcvd: 108
```

## DNS Zone Transfer

DNS zone transfer, also sometimes known by the inducing DNS query type AXFR, is a type of DNS transaction. It is one of the mechanisms used to replicate DNS database across a set of DNS servers.

As we know that every domain has some authoritative name servers associated with it. For eg in the case of google.com, the nameservers were ns1.google.com to ns4.google.com .These Nameservers are used for handling requests related to the domain google.com. Let’s say we have a domain example.com and it has it’s two nameservers as ns1.example.com and ns2.example.com. Usually a big organization will have more than one nameservers so that if one goes down for some time, the other one is ready to back it up and handle the requests. Usually one of these servers will be the Master server and the other one will be the slave server. Hence to stay in sync with each other, the slave server must query the Master server and fetch the latest records after a specific period of time. The Master server will provide the slave server with all the information it has. This is basically what is called a “Zone Transfer”.

A properly configured nameserver should only be allowed to serve requests of Zone transfer from other Nameservers of the same domain. However if the server is not configured properly it will serve all requests of Zone transfer made to it without checking the querying client. This leads to leakage of valuable information. DNS Zone transfer is sometimes referred through it’s opcode mnemonic AXFR.

### Initiating ZONE Transfer

The zone transfer can be initiated by sending axfr request to a DNS server regarding of a given domain. For demo perpose we can use zonetransfer.me which allows the zone transfer. The steps will be: first get all the name servers for given domain, then try the dig command for zone transfer with their name servers.

Get the name servers of zonetransfer.me

```shell
$ dig zonetransfer.me NS

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> zonetransfer.me NS
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 48237
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;zonetransfer.me.		IN	NS

;; ANSWER SECTION:
zonetransfer.me.	7200	IN	NS	nsztm2.digi.ninja.
zonetransfer.me.	7200	IN	NS	nsztm1.digi.ninja.

;; Query time: 978 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Tue May 19 15:50:56 IST 2020
;; MSG SIZE  rcvd: 96
```

The name servers are : `nsztm1.digi.ninja` and `nsztm2.digi.ninja`. Now request for zone transfer to one of these name servers, the syntax is :

```shell
$ dig axfr @NameServer victim.com
```

Example :

```shell
$ dig axfr @nsztm1.digi.ninja zonetransfer.me
; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> axfr @nsztm1.digi.ninja zonetransfer.me
; (1 server found)
;; global options: +cmd
zonetransfer.me.	7200	IN	SOA	nsztm1.digi.ninja. robin.digi.ninja. 2019100801 172800 900 1209600 3600
zonetransfer.me.	300	IN	HINFO	"Casio fx-700G" "Windows XP"
zonetransfer.me.	301	IN	TXT	"google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"
zonetransfer.me.	7200	IN	MX	0 ASPMX.L.GOOGLE.COM.
zonetransfer.me.	7200	IN	MX	10 ALT1.ASPMX.L.GOOGLE.COM.
zonetransfer.me.	7200	IN	MX	10 ALT2.ASPMX.L.GOOGLE.COM.
zonetransfer.me.	7200	IN	MX	20 ASPMX2.GOOGLEMAIL.COM.
zonetransfer.me.	7200	IN	MX	20 ASPMX3.GOOGLEMAIL.COM.
zonetransfer.me.	7200	IN	MX	20 ASPMX4.GOOGLEMAIL.COM.
zonetransfer.me.	7200	IN	MX	20 ASPMX5.GOOGLEMAIL.COM.
zonetransfer.me.	7200	IN	A	5.196.105.14
zonetransfer.me.	7200	IN	NS	nsztm1.digi.ninja.
zonetransfer.me.	7200	IN	NS	nsztm2.digi.ninja.
_acme-challenge.zonetransfer.me. 301 IN	TXT	"6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"
_sip._tcp.zonetransfer.me. 14000 IN	SRV	0 0 5060 www.zonetransfer.me.
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me. 7200	IN PTR www.zonetransfer.me.
asfdbauthdns.zonetransfer.me. 7900 IN	AFSDB	1 asfdbbox.zonetransfer.me.
asfdbbox.zonetransfer.me. 7200	IN	A	127.0.0.1
asfdbvolume.zonetransfer.me. 7800 IN	AFSDB	1 asfdbbox.zonetransfer.me.
canberra-office.zonetransfer.me. 7200 IN A	202.14.81.230
cmdexec.zonetransfer.me. 300	IN	TXT	"; ls"
contact.zonetransfer.me. 2592000 IN	TXT	"Remember to call or email Pippa on +44 123 4567890 or pippa@zonetransfer.me when making DNS changes"
dc-office.zonetransfer.me. 7200	IN	A	143.228.181.132
deadbeef.zonetransfer.me. 7201	IN	AAAA	dead:beaf::
dr.zonetransfer.me.	300	IN	LOC	53 20 56.558 N 1 38 33.526 W 0.00m 1m 10000m 10m
DZC.zonetransfer.me.	7200	IN	TXT	"AbCdEfG"
email.zonetransfer.me.	2222	IN	NAPTR	1 1 "P" "E2U+email" "" email.zonetransfer.me.zonetransfer.me.
email.zonetransfer.me.	7200	IN	A	74.125.206.26
Hello.zonetransfer.me.	7200	IN	TXT	"Hi to Josh and all his class"
home.zonetransfer.me.	7200	IN	A	127.0.0.1
Info.zonetransfer.me.	7200	IN	TXT	"ZoneTransfer.me service provided by Robin Wood - robin@digi.ninja. See http://digi.ninja/projects/zonetransferme.php for more information."
internal.zonetransfer.me. 300	IN	NS	intns1.zonetransfer.me.
internal.zonetransfer.me. 300	IN	NS	intns2.zonetransfer.me.
intns1.zonetransfer.me.	300	IN	A	81.4.108.41
intns2.zonetransfer.me.	300	IN	A	167.88.42.94
office.zonetransfer.me.	7200	IN	A	4.23.39.254
ipv6actnow.org.zonetransfer.me.	7200 IN	AAAA	2001:67c:2e8:11::c100:1332
owa.zonetransfer.me.	7200	IN	A	207.46.197.32
robinwood.zonetransfer.me. 302	IN	TXT	"Robin Wood"
rp.zonetransfer.me.	321	IN	RP	robin.zonetransfer.me. robinwood.zonetransfer.me.
sip.zonetransfer.me.	3333	IN	NAPTR	2 3 "P" "E2U+sip" "!^.*$!sip:customer-service@zonetransfer.me!" .
sqli.zonetransfer.me.	300	IN	TXT	"' or 1=1 --"
sshock.zonetransfer.me.	7200	IN	TXT	"() { :]}; echo ShellShocked"
staging.zonetransfer.me. 7200	IN	CNAME	www.sydneyoperahouse.com.
alltcpportsopen.firewall.test.zonetransfer.me. 301 IN A	127.0.0.1
testing.zonetransfer.me. 301	IN	CNAME	www.zonetransfer.me.
vpn.zonetransfer.me.	4000	IN	A	174.36.59.154
www.zonetransfer.me.	7200	IN	A	5.196.105.14
xss.zonetransfer.me.	300	IN	TXT	"'><script>alert('Boo')</script>"
zonetransfer.me.	7200	IN	SOA	nsztm1.digi.ninja. robin.digi.ninja. 2019100801 172800 900 1209600 3600
;; Query time: 181 msec
;; SERVER: 81.4.108.41#53(81.4.108.41)
;; WHEN: Tue May 19 15:55:05 IST 2020
;; XFR size: 50 records (messages 1, bytes 1994)
```

A zone transfer reveals a lot of information about the domain. This forms a very important part of the “Information Gathering” stage during a penetration test, vulnerability assessment etc. We can figure out a lot of things by looking at the dump.For e.g. we can find different subdomains. Some of them might be running on different servers.Those server may not be fully patched and hence be vulnerable.

To protect your nameservers from leaking valuable information, one must allow zone transfer to other nameservers of the same domain only. For e.g. ns1.example.com should allow zone transfer to ns2.example.com only and discard all the other requests.

For more detailed analysis of above generated report please look at https://digi.ninja/projects/zonetransferme.php
