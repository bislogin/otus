# Лабороторная работа на тему:    
## Архитектура сети IPv4/v6  

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab03/Topology.png)

###  Задание:
Разработь и задокументировать адресное пространство для лабораторного стенда.
Настроить ip адреса на каждом активном порту
Настроить каждый VPC в каждом офисе в своем VLAN.
Настроить VLAN/Loopbackup interface управления для сетевых устройств


#### Разработь и задокументировать адресное пространство для лабораторного стенда.

|msk |	10.10.0.0/16|||2001:AAAA::/64	|||  
|---|---|---|---|---|---|---|
|	|ip|	mask|	gw|	ipv6|	pref|	LL|	
|R12|	10.10.0.1	|32|		|2001:AAAA::1	|128|	FE80::1	|
|e0/0|	10.10.0.100	|31|		|2001:AAAA::64	|127|	FE80::64	|
|e0/1|	10.10.0.102	|31|		|2001:AAAA::66	|127|	FE80::66	|
|e0/2|	10.10.0.104	|31|		|2001:AAAA::68	|127|	FE80::68	|
|e0/3|	10.10.0.106	|31|		|2001:AAAA::5A	|127	|FE80::5A	|		
|	e1/0	|	10.10.0.126	|	31	|		|	2001:AAAA::7E	|	127	|	FE80::7E	|
|		|		|		|		|		|		|		|
|	R13	|	10.10.0.2	|	32	|		|	2001:AAAA::2	|	128	|	FE80::2	|
|	e0/0	|	10.10.0.108	|	31	|		|	2001:AAAA::6C	|	127	|	FE80::6C	|
|	e0/1	|	10.10.0.110	|	31	|		|	2001:AAAA::6E	|	127	|	FE80::6E	|
|	e0/2	|	10.10.0.112	|	31	|		|	2001:AAAA::70	|	127	|	FE80::70	|
|	e0/3	|	10.10.0.114	|	31	|		|	2001:AAAA::72	|	127	|	FE80::72	|
|	e1/0	|	10.10.0.127	|	31	|		|	2001:AAAA::7F	|	127	|	FE80::7F	|
|		|		|		|		|		|		|		|
|	R14	|	10.10.0.3	|	32	|		|	2001:AAAA::3	|	128	|	FE80::3	|
|	e0/0	|	10.10.0.105	|	31	|		|	2001:AAAA::69	|	127	|	FE80::69	|
|	e0/1	|	10.10.0.115	|	31	|		|	2001:AAAA::73	|	127	|	FE80::73	|
|	e0/2	|	100.100.10.1	|	30	|		|	2001:AAAA:AAFF::1	|	64	|	FE80::AF	|
|	e0/3	|	10.10.0.116	|	31	|		|	2001:AAAA::74	|	127	|	FE80::74	|
|	e1/0	|	10.10.0.124	|	31	|		|	2001:AAAA::7C	|	127	|	FE80::7C	|
|		|		|		|		|		|		|		|
|	R15	|	10.10.0.4	|	32	|		|	2001:AAAA::4	|	128	|	FE80::4	|
|	e0/0	|	10.10.0.113	|	31	|		|	2001:AAAA::71	|	127	|	FE80::71	|
|	e0/1	|	10.10.0.107	|	31	|		|	2001:AAAA::6B	|	127	|	FE80::6B	|
|	e0/2	|	100.100.11.1	|	30	|		|	2001:AAAA:AAEE::1	|	64	|	FE80::AE	|
|	e0/3	|	10.10.0.118	|	31	|		|	2001:AAAA::76	|	127	|	FE80::76	|
|	e1/0	|	10.10.0.125	|	31	|		|	2001:AAAA::7D	|	127	|	FE80::7D	|
|		|		|		|		|		|		|		|
|	R19	|	10.10.0.5	|	32	|		|	2001:AAAA::5	|	128	|	FE80::5	|
|	e0/0	|	10.10.0.117	|	31	|		|	2001:AAAA::75	|	127	|	FE80::75	|
|		|		|		|		|		|		|		|
|	R20	|	10.10.0.6	|	32	|		|	2001:AAAA::6	|	128	|	FE80::6	|
|	e0/0	|	10.10.0.119	|	31	|		|	2001:AAAA::77	|	127	|	FE80::77	|
|		|		|		|		|		|		|		|
|	SW2	|	10.10.0.7	|	32	|		|	2001:AAAA::7	|	128	|	FE80::7	|
|	e0/0	|	10.10.0.128	|	31	|		|	2001:AAAA::80	|	127	|	FE80::80	|
|	e0/1	|	10.10.0.130	|	31	|		|	2001:AAAA::82	|	127	|	FE80::82	|
|	vlan 70	|	172.10.70.250	|	24	|	172.10.70.254	|	2001:AAAA::70:FA	|	112	|	FE80::70:FA	|
|		|		|		|		|		|		|		|
|	SW3	|	10.10.0.8	|	32	|		|	2001:AAAA::8	|	128	|	FE80::8	|
|	e0/0	|	10.10.0.132	|	31	|		|	2001:AAAA::84	|	127	|	FE80::84	|
|	e0/1	|	10.10.0.134	|	31	|		|	2001:AAAA::86	|	127	|	FE80::86	|
|	vlan 10	|	172.10.10.250	|	24	|	172.10.10.254	|	2001:AAAA::10:FA	|	112	|	FE80::10:FA	|
|		|		|		|		|		|		|		|
|	SW4	|	10.10.0.9	|	32	|		|	2001:AAAA::9	|	128	|	FE80::9	|
|	e0/0	|	10.10.0.133	|	31	|		|	2001:AAAA::85	|	127	|	FE80::85	|
|	e0/1	|	10.10.0.131	|	31	|		|	2001:AAAA::83	|	127	|	FE80::83	|
|	e0/2	|	10.10.0.120	|	31	|		|	2001:AAAA::78	|	127	|	FE80::78	|
|	e0/3	|	10.10.0.122	|	31	|		|	2001:AAAA::7A	|	127	|	FE80::7A	|
|	e1/0	|	10.10.0.101	|	31	|		|	2001:AAAA::65	|	127	|	FE80::65	|
|	e1/1	|	10.10.0.111	|	31	|		|	2001:AAAA::6F	|	127	|	FE80::6F	|
|	vlan10	|	172.10.10.252	|	24	|		|	2001:AAAA::10:FC	|	112	|	FE80::10:FC	|
|	vlan70	|	172.10.70.252	|	24	|		|	2001:AAAA::70:FC	|	112	|	FE80::70:FC	|
|	VRRP10	|	172.10.10.254	|	24	|		|	2001:AAAA::10:FE	|	112	|	FE80::10:FE	|
|	VRRP70	|	172.10.70.254	|	24	|		|	2001:AAAA::70:FE	|	112	|	FE80::70:FE	|
|		|		|		|		|		|		|		|
|	SW5	|	10.10.0.10	|	32	|		|	2001:AAAA::10	|	128	|	FE80::10	|
|	e0/0	|	10.10.0.129	|	31	|		|	2001:AAAA::81	|	127	|	FE80::81	|
|	e0/1	|	10.10.0.135	|	31	|		|	2001:AAAA::87	|	127	|	FE80::87	|
|	e0/2	|	10.10.0.121	|	31	|		|	2001:AAAA::79	|	127	|	FE80::79	|
|	e0/3	|	10.10.0.123	|	31	|		|	2001:AAAA::7B	|	127	|	FE80::7B	|
|	e1/0	|	10.10.0.109	|	31	|		|	2001:AAAA::6D	|	127	|	FE80::6D	|
|	e1/1	|	10.10.0.103	|	31	|		|	2001:AAAA::67	|	127	|	FE80::67	|
|	e0/0	|	10.10.0.	|	31	|		|	2001:AAAA::	|	127	|	FE80::	|
|	e0/1	|	10.10.0.	|	31	|		|	2001:AAAA::	|	127	|	FE80::	|
|	vlan10	|	172.10.10.253	|	24	|		|	2001:AAAA::10:FD	|	112	|	FE80::10:FD	|
|	vlan70	|	172.10.70.253	|	24	|		|	2001:AAAA::70:FD	|	112	|	FE80::70:FD	|
|	VRRP10	|	172.10.10.254	|	24	|		|	2001:AAAA::10:FE	|	112	|	FE80::10:FE	|
|	VRRP70	|	172.10.70.254	|	24	|		|	2001:AAAA::70:FE	|	112	|	FE80::70:FE	|
|		|		|		|		|		|		|		|
|	VPC1	|	172.10.10.1	|	24	|	172.10.10.254	|	2001:AAAA::10:1	|	112	|		|
|		|		|		|		|		|		|		|
|	VPC7	|	172.10.70.7	|	24	|	172.10.70.254	|	2001:AAAA::70:7	|	112	|		|
|		|		|		|		|		|		|		|
|		|		|		|		|		|		|		|
|	спб	|	10.20.0.0/16	|		|		|	2001:BBBB::/64	|		|		|
|		|	ip	|	mask	|	gw	|	ipv6	|	pref	|	LL	|
|	R16	|	10.20.0.1	|	32	|		|	2001:BBBB::1	|	128	|	FE80::1	|
|	e0/0	|	10.20.0.100	|	31	|		|	2001:BBBB::64	|	127	|	FE80::64	|
|	e0/1	|	10.20.0.102	|	31	|		|	2001:BBBB::66	|	127	|	FE80::66	|
|	e0/2	|	10.20.0.104	|	31	|		|	2001:BBBB::68	|	127	|	FE80::68	|
|	e0/3	|	10.20.0.106	|	31	|		|	2001:BBBB::5A	|	127	|	FE80::5A	|
|	e1/0	|	10.20.0.121	|	31	|		|	2001:BBBB::79	|	127	|	FE80::79	|
|		|		|		|		|		|		|		|
|	R17	|	10.20.0.2	|	32	|		|	2001:BBBB::2	|	128	|	FE80::2	|
|	e0/0	|	10.20.0.108	|	31	|		|	2001:BBBB::6C	|	127	|	FE80::6C	|
|	e0/1	|	10.20.0.110	|	31	|		|	2001:BBBB::6E	|	127	|	FE80::6E	|
|	e0/2	|	10.20.0.112	|	31	|		|	2001:BBBB::70	|	127	|	FE80::70	|
|	e0/3	|	10.20.0.118	|	31	|		|	2001:BBBB::76	|	127	|	FE80::76	|
|	e1/0	|	10.20.0.120	|	31	|		|	2001:BBBB::78	|	127	|	FE80::78	|
|		|		|		|		|		|		|		|
|	R18	|	10.20.0.3	|	32	|		|	2001:BBBB::3	|	128	|	FE80::3	|
|	e0/0	|	10.20.0.103	|	31	|		|	2001:BBBB::67	|	127	|	FE80::67	|
|	e0/1	|	10.20.0.111	|	31	|		|	2001:BBBB::6F	|	127	|	FE80::6F	|
|	e0/2	|	100.100.21.1	|	30	|		|	2001:BBBB:BBD1::1	|	64	|	FE80::BD1	|
|	e0/3	|	100.100.22.1	|	30	|		|	2001:BBBB:BBD2::1	|	64	|	FE80::BD2	|
|		|		|		|		|		|		|		|
|	R32	|	10.20.0.4	|	32	|		|	2001:BBBB::4	|	128	|	FE80::4	|
|	e0/0	|	10.20.0.107	|	31	|		|	2001:BBBB::6B	|	127	|	FE80::6B	|
|	e0/1	|	10.20.0.119	|	31	|		|	2001:BBBB::77	|	127	|	FE80::77	|
|		|		|		|		|		|		|		|
|	SW9	|	10.20.0.5	|	32	|		|	2001:BBBB::5	|	128	|	FE80::5	|
|	e0/0	|	10.20.0.114	|	31	|		|	2001:BBBB::72	|	127	|	FE80::72	|
|	e0/1	|	10.20.0.116	|	31	|		|	2001:BBBB::74	|	127	|	FE80::74	|
|	e0/3	|	10.20.0.109	|	31	|		|	2001:BBBB::6D	|	127	|	FE80::6D	|
|	e1/0	|	10.20.0.105	|	31	|		|	2001:BBBB::69	|	127	|	FE80::69	|
|	vlan80	|	172.10.80.254	|	24	|		|	2001:BBBB::80:FE	|	112	|	FE80::80:FE	|
|		|		|		|		|		|		|		|
|	SW10	|	10.20.0.6	|	32	|		|	2001:BBBB::6	|	128	|	FE80::6	|
|	e0/0	|	10.20.0.115	|	31	|		|	2001:BBBB::73	|	127	|	FE80::73	|
|	e0/1	|	10.20.0.117	|	31	|		|	2001:BBBB::75	|	127	|	FE80::75	|
|	e0/3	|	10.20.0.101	|	31	|		|	2001:BBBB::65	|	127	|	FE80::65	|
|	e1/0	|	10.20.0.113	|	31	|		|	2001:BBBB::71	|	127	|	FE80::71	|
|	vlan100	|	172.10.100.254	|	24	|		|	2001:BBBB::100 :FE	|	112	|	FE80::100 :FE	|
|		|		|		|		|		|		|		|
|	VPC	|	172.10.100.1	|	24	|	172.10.100.254	|	2001:BBBB::100 :1	|	112	|		|
|		|		|		|		|		|		|		|
|	VPC8	|	172.10.80.1	|	24	|	172.10.80.254	|	2001:BBBB::80:1	|	112	|		|
|		|		|		|		|		|		|		|
|		|		|		|		|		|		|		|
|	Чокурдах	|	10.30.0.0/16	|		|		|	2001:CCCC:1::/64	|		|		|
|		|	ip	|	mask	|	gw	|	ipv6	|	pref	|	LL	|
|	R28	|	10.30.0.1	|	32	|		|	2001:CCCC:1::1	|	128	|	FE80::1	|
|	e0/0	|	100.100.30.1	|	30	|		|	2001:CCCC:1:CCD1::1	|	64	|	FE80::CD1	|
|	e0/1	|	100.100.31.1	|	30	|		|	2001:CCCC:1:CCD2::1	|	64	|	FE80::CD2	|
|	e0/2	|	10.30.0.100	|	31	|		|	2001:CCCC:1::64	|	127	|	FE80::64	|
|		|		|		|		|		|		|		|
|	SW29	|	10.30.0.2	|	32	|		|	2001:CCCC:1::2	|	128	|	FE80::2	|
|	e0/2	|	10.30.0.101	|	31	|		|	2001:CCCC:1::65	|	127	|	FE80::65	|
|	vlan30	|	172.10.30.254	|	24	|		|	2001:CCCC:1::30:FE	|	112	|	FE80::30:FE	|
|	vlan31	|	172.10.31.254	|	24	|		|	2001:CCCC:1::31:FE	|	112	|	FE80::31:FE	|
|		|		|		|		|		|		|		|
|	VPC30	|	172.10.30.1	|	24	|	172.10.30.254	|	2001:CCCC:1::30:1	|	112	|		|
|		|		|		|		|		|		|		|
|	VPC31	|	172.10.31.1	|	24	|	172.10.31.254	|	2001:CCCC:1::31:1	|	112	|		|
|		|		|		|		|		|		|		|
|		|		|		|		|		|		|		|
|	Лабытнаги	|	10.35.0.0/16	|		|		|	2001:CCCC:2::/64	|		|		|
|		|	ip	|	mask	|	gw	|	ipv6	|	pref	|	LL	|
|	R27	|	10.35.0.1	|	32	|		|	2001:CCCC:2::1	|	128	|	FE80::1	|
|	e0/0	|	100.100.35.1	|	30	|		|	2001:CCCC:2:CCDD::1	|	64	|	FE80::CD1	|
|		|		|		|		|		|		|		|
|		|		|		|		|		|		|		|
|	Триада	|	10.40.0.0/16	|		|		|	2001:DDDD::/64	|		|		|
|		|	ip	|	mask	|	gw	|	ipv6	|	pref	|	LL	|
|	R23	|	10.40.0.1	|	32	|		|	2001:DDDD::1	|	128	|	FE80::1	|
|	e0/0	|	100.100.40.1	|	30	|		|	2001:DDDD:DDFF::1	|	64	|	FE80::1	|
|	e0/1	|	10.40.0.100	|	31	|		|	2001:DDDD::64	|	127	|	FE80::64	|
|	e0/2	|	10.40.0.102	|	31	|		|	2001:DDDD::66	|	127	|	FE80::66	|
|		|		|		|		|		|		|		|
|	R24	|	10.40.0.2	|	32	|		|	2001:DDDD::2	|	128	|	FE80::2	|
|	e0/0	|	100.100.40.5	|	30	|		|	2001:DDDD:DDEE:1::5	|	64	|	FE80::5	|
|	e0/1	|	10.40.0.104	|	31	|		|	2001:DDDD::68	|	127	|	FE80::68	|
|	e0/2	|	10.40.0.103	|	31	|		|	2001:DDDD::67	|	127	|	FE80::67	|
|	e0/3	|	100.100.21.2	|	30	|		|	2001:BBBB:BBD1::2	|	64	|	FE80::DB2	|
|	e1/0	|	10.40.0.108	|	31	|		|	2001:DDDD::6C	|	127	|	FE80::6C	|
|		|		|		|		|		|		|		|
|	R25	|	10.40.0.3	|	32	|		|	2001:DDDD::3	|	128	|	FE80::3	|
|	e0/0	|	10.40.0.101	|	31	|		|	2001:DDDD::65	|	127	|	FE80::65	|
|	e0/1	|	100.100.35.2	|	30	|		|	2001:CCCC:2:CCDD::2	|	64	|	FE80::DC2	|
|	e0/2	|	10.40.0.106	|	31	|		|	2001:DDDD::5A	|	127	|	FE80::5A	|
|	e0/3	|	100.100.31.2	|	30	|		|	2001:CCCC:1:CCD2::2	|	64	|	FE80::DC1	|
|	e1/0	|	10.40.0.109	|	31	|		|	2001:DDDD::6D	|	127	|	FE80::6D	|
|		|		|		|		|		|		|		|
|	R26	|	10.40.0.4	|	32	|		|	2001:DDDD::4	|	128	|	FE80::4	|
|	e0/0	|	10.40.0.105	|	31	|		|	2001:DDDD::69	|	127	|	FE80::69	|
|	e0/1	|	100.100.30.2	|	30	|		|	2001:CCCC:1:CCD1::2	|	64	|	FE80::DC1	|
|	e0/2	|	10.40.0.107	|	31	|		|	2001:DDDD::5B	|	127	|	FE80::5B	|
|	e0/3	|	100.100.22.2	|	30	|		|	2001:BBBB:BBD2::2	|	64	|	FE80::DB2	|
|		|		|		|		|		|		|		|
|		|		|		|		|		|		|		|
|	Ламас	|	10.50.0.0/16	|		|		|	2001:EEEE::/64	|		|		|
|		|	ip	|	mask	|	gw	|	ipv6	|	pref	|	LL	|
|	R21	|	10.50.0.1	|	32	|		|	2001:EEEE::1	|	128	|	FE80::1	|
|	e0/0	|	100.100.11.2	|	30	|		|	2001:AAAA:AAEE::2	|	64	|	FE80::EA	|
|	e0/1	|	100.100.50.1	|	30	|		|	2001:EEEE:EEFF::1	|	64	|	FE80::EF	|
|	e0/2	|	100.100.40.6	|	30	|		|	2001:DDDD:DDEE:1::6	|	64	|	FE80::6	|
|		|		|		|		|		|		|		|
|	Киторн 	|	10.60.0.0/16	|		|		|	2001:FFFF::/64	|		|		|
|		|	ip	|	mask	|	gw	|	ipv6	|	pref	|	LL	|
|	R22	|	10.60.0.1	|	32	|		|	2001:FFFF::1	|	128	|	FE80::1	|
|	e0/0	|	100.100.10.2	|	30	|		|	2001:AAAA:AAFF::2	|	64	|	FE80::FA	|
|	e0/1	|	100.100.50.2	|	30	|		|	2001:EEEE:EEFF::2	|	64	|	FE80::FE	|
|	e0/2	|	100.100.40.2	|	30	|		|	2001:DDDD:DDFF::2	|	64	|	FE80::FD	|


#### Настроить ip адреса на каждом активном порту

Настройка роутера, на примере R22

Начальные настроки:
```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)# hostname R22 
R22(config)# no ip domain name
R22(config)# enable secret class
R22(config)# line con 0 
R22(config-line)# password cisco
R22(config-line)# login 
R22(config-line)# logging synchronous
R22(config-line)# line vty 0 4
R22(config-line)# password cisco
R22(config-line)# login
R22(config-line)# logging synchronous
R22(config-line)# exit
R22(config)# service password-encryption
R22(config)# Banner motd 'This is a secure system. Authorized Access Only!'
R22(config)# clock timezone MSK +3
R22(config)# ipv6 unicast-routing
```
#### Настройка Loopbackup interface для управления:
```
R22(config)# interface lo0 
R22(config-if)# description == FOR MGMT == 
R22(config-if)# ip address 10.60.0.1 255.255.255.255 
R22(config-if)# ipv6 address FE80::1 link-local 
R22(config-if)# ipv6 address 2001:FFFF::1/128 
R22(config-if)# ipv6 enable
R22(config-if)# no shutdown
R22(config-if)# exit
```
#### Настройка всех активных портов:
```
R22(config)# 
R22(config)#  interface e0/0 
R22(config-if)# description == link to R14 == 
R22(config-if)# ip address 100.100.10.2 255.255.255.252 
R22(config-if)# ipv6 address FE80::FA link-local 
R22(config-if)# ipv6 address 2001:AAAA:AAFF::2/64 
R22(config-if)# ipv6 enable
R22(config-if)# no shutdown
R22(config-if)# exit
R22(config)# 
*Oct 17 12:38:53.143: %LINK-3-UPDOWN: Interface Ethernet0/0, changed state to up
*Oct 17 12:38:54.147: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to up
R22(config)#  interface e0/1 
R22(config-if)# description == link to R21 == 
R22(config-if)# ip address 100.100.50.2 255.255.255.252 
R22(config-if)# ipv6 address FE80::FE link-local 
R22(config-if)# ipv6 address 2001:EEEE:EEFF::2/64 
R22(config-if)# ipv6 enable
R22(config-if)# no shutdown
R22(config-if)# exit
R22(config)# 
*Oct 17 12:39:38.243: %LINK-3-UPDOWN: Interface Ethernet0/1, changed state to up
*Oct 17 12:39:39.247: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to up
R22(config)#  interface e0/2 
R22(config-if)# description == link to R23 == 
R22(config-if)# ip address 100.100.40.2 255.255.255.252 
R22(config-if)# ipv6 address FE80::FD link-local 
R22(config-if)# ipv6 address 2001:DDDD:DDFF::2/64 
R22(config-if)# ipv6 enable
R22(config-if)# no shutdown
R22(config-if)# exit
R22(config)# do wr
Building configuration...
[OK]
R22(config)# 
```
