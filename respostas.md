### -1-
; <<>> DiG 9.20.11-4-Debian <<>> @localhost www.edu.xunta.es
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 929
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: e76bc210203604870100000068e375cc8e818bd67fc93eb1 (good)
;; QUESTION SECTION:
;www.edu.xunta.es.		IN	A

;; ANSWER SECTION:
www.edu.xunta.es.	1142	IN	CNAME	edu.xunta.es.
edu.xunta.es.		21431	IN	A	85.91.64.65

;; Query time: 64 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Mon Oct 06 07:54:52 UTC 2025
;; MSG SIZE  rcvd: 103

### -2-
*Arquivo "/etc/bind/named.conf.options":*

options {
	directory "/var/cache/bind";
	forwarders {
		8.8.8.8;
		9.9.9.9;
	};
};

Saída do comando *dig @localhost www.mecd.gob.es*:

; <<>> DiG 9.20.11-4-Debian <<>> @localhost www.mecd.gob.es
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 22417
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: a2500bfa3c5776900100000068e3762396ae72b06032fcf8 (good)
;; QUESTION SECTION:
;www.mecd.gob.es.		IN	A

;; ANSWER SECTION:
www.mecd.gob.es.	21600	IN	A	212.128.114.116

;; Query time: 67 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Mon Oct 06 07:56:19 UTC 2025
;; MSG SIZE  rcvd: 88

### -3-
*Arquivo de zona:*

$TTL	604800
@	IN	SOA	darthvader.starwars.lan. admin.starwars.lan. (
	2025100601	;	Serial
	604800	;	Refresh
	86400	;	Retry
	2419200	;	Expire
	604800 )	;	Negative Cache TTL
	
@	IN	NS	darthvader.starwars.lan.
@	IN	NS	darthsidious.starwars.lan.

@	IN	A	192.168.20.200
darthvader	IN	A	192.168.20.10
skywalker	IN	A	192.168.20.101
skywalker	IN	A	192.168.20.111
luke	IN	A	192.168.20.22
darthsidious	IN	A	192.168.20.11
yoda	IN	A	192.168.20.24
yoda	IN	A	192.168.20.25
c3p0	IN	A	192.168.20.26
palpatine	IN	CNAME	darthsidious
c3p0	IN	MX	10	c3p0.starwars.lan.
lenda	IN	TXT	"Que a forza te acompanhe"


*Arquivo "/etc/bind/named.conf.local":*

zone "starwars.lan" {
	type master;
	file "/etc/bind/db.starwars.lan";
};

### -4-
*Arquivo de zona inversa:*

$TTL	604800
@	IN	SOA	darthvader.starwars.lan. admin.starwars.lan. (
	2025100601	;	Serial
	604800	;	Refresh
	86400	;	Retry
	2419200	;	Expire
	604800 )	;	Negative Cache TTL
	
@	IN	NS	darthvader.starwars.lan.
@	IN	NS	darthsidious.starwars.lan.

10	IN	PTR darthvader.starwars.lan.
11	IN	PTR	darthsidious.starwars.lan.
22	IN	PTR	luke.starwars.lan.
24	IN	PTR	yoda.starwars.lan.
25	IN	PTR	yoda.starwars.lan.
26	IN	PTR	c3p0.starwars.lan.
101	IN	PTR	skywalker.starwars.lan.
111	IN	PTR	skywalker.starwars.lan.

*Arquivo "/etc/bind/named.conf.local":*

zone "starwars.lan" {
	type master;
	file "/etc/bind/db.starwars.lan";
};
zone "20.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192.168.20";
};

### -5-
> *nslookup darthvader.starwars.lan localhost*
Server:		localhost
Address:	127.0.0.1#53

Name:	darthvader.starwars.lan
Address: 192.168.20.10

> *nslookup skywalker.starwars.lan localhost*
Server:		localhost
Address:	127.0.0.1#53

Name:	skywalker.starwars.lan
Address: 192.168.20.111
Name:	skywalker.starwars.lan
Address: 192.168.20.101

> *nslookup starwars.lan localhost*
Server:		localhost
Address:	127.0.0.1#53

Name:	starwars.lan
Address: 192.168.20.200

*nslookup -q=mx starwars.lan localhost*
Server:		localhost
Address:	127.0.0.1#53

*** Can't find starwars.lan: No answer

*nslookup -q=ns starwars.lan localhost*
Server:		localhost
Address:	127.0.0.1#53

starwars.lan	nameserver = darthvader.starwars.lan.
starwars.lan	nameserver = darthsidious.starwars.lan.

> *nslookup -q=soa starwars.lan localhost*
Server:		localhost
Address:	127.0.0.1#53

starwars.lan
	origin = darthvader.starwars.lan
	mail addr = admin.starwars.lan
	serial = 2025100601
	refresh = 604800
	retry = 86400
	expire = 2419200
	minimum = 604800

*nslookup -q=txt lenda.starwars.lan localhost*
Server:		localhost
Address:	127.0.0.1#53

lenda.starwars.lan	text = "Que a forza te acompanhe"

> *nslookup 192.168.20.11 localhost*
11.20.168.192.in-addr.arpa	name = darthsidious.starwars.lan.
