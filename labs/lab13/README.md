# Лабороторная работа на тему:    
## IPSec over DmVPN

### Цель:
Настроить GRE поверх IPSec между офисами Москва и С.-Петербург   
Настроить DMVPN поверх IPSec между офисами Москва и Чокурдах, Лабытнанги     

### Описание/Пошаговая инструкция выполнения домашнего задания:
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:   

1. Настроите GRE поверх IPSec между офисами Москва и С.-Петербург.
2. Настроите DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги.  
Все узлы в офисах в лабораторной работе должны иметь IP связность.    

Настроим центр сертификаций на R15
```
ip domain-name MSK.ru
ip http server
crypto key generate rsa general-keys label CA exportable modulus 2048
crypto pki server CA
 database level complete
 no shut
```
Клиенты (VPN peers)   
```
crypto key generate rsa label VPN modulus 2048
crypto pki trustpoint VPN
 enrollment url http://192.168.10.1:80
 revocation-check none
(config)#crypto pki authenticate VPN
(config)#crypto pki enroll VPN 
```

На CA подпишем сертификаты
```
show crypto pki server CA requests
crypto pki server CA grant [1-999|all]
```

#### 1. Настроите GRE поверх IPSec между офисами Москва и С.-Петербург.
Настроим первую фазу IKEv2 на R15
```
crypto ikev2 proposal PROSPB 
 encryption aes-cbc-128
 integrity md5
 group 2
crypto ikev2 policy IKEV2 
 proposal PROSPB
crypto ikev2 profile PROFILE1
 match address local interface Loopback100
 match identity remote address 0.0.0.0 
 authentication remote rsa-sig
 authentication local rsa-sig
 pki trustpoint VPN
```
Настроим вторую фазу
```
crypto ipsec transform-set IPSEC_TS esp-aes esp-md5-hmac 
 mode tunnel
crypto ipsec profile IPSEC_PROF
 set transform-set IPSEC_TS 
 set ikev2-profile PROFILE1
```

На R18
```
crypto ikev2 proposal PROMSK 
 encryption aes-cbc-128
 integrity md5
 group 2
crypto ikev2 policy IKEV2 
 proposal PROMSK
crypto ikev2 profile PROFILE1
 match address local interface Loopback18
 match identity remote address 0.0.0.0 
 authentication remote rsa-sig
 authentication local rsa-sig
 pki trustpoint VPN
crypto ipsec transform-set IPSEC_TS esp-aes esp-md5-hmac 
 mode tunnel
crypto ipsec profile IPSEC_PROF
 set transform-set IPSEC_TS 
 set ikev2-profile PROFILE1

```
На тунельном интерфейте 
```
interface Tunnel1020
 ip address 192.168.10.1 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source Loopback100
 tunnel destination 10.20.20.6
 tunnel protection ipsec profile IPSEC_PROF
```

Провери пинг из МСК в Питер
```
VPC#ping 172.10.80.1 
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.10.80.1, timeout is 2 seconds:
!!!!!
```
Первая фаза
```
R15#show crypto ikev2 sa
 IPv4 Crypto IKEv2  SA 

Tunnel-id Local                 Remote                fvrf/ivrf            Status 
1         10.10.10.100/500      10.20.20.6/500        none/none            READY  
      Encr: AES-CBC, keysize: 128, Hash: MD596, DH Grp:2, Auth sign: RSA, Auth verify: RSA
      Life/Active Time: 86400/21 sec

 IPv6 Crypto IKEv2  SA 
```
Вторая фаза
```
R15#show crypto ipsec sa

interface: Tunnel1020
    Crypto map tag: Tunnel1020-head-0, local addr 10.10.10.100

   protected vrf: (none)
   local  ident (addr/mask/prot/port): (10.10.10.100/255.255.255.255/47/0)
   remote ident (addr/mask/prot/port): (10.20.20.6/255.255.255.255/47/0)
   current_peer 10.20.20.6 port 500
     PERMIT, flags={origin_is_acl,}
    #pkts encaps: 223, #pkts encrypt: 223, #pkts digest: 223
    #pkts decaps: 211, #pkts decrypt: 211, #pkts verify: 211
    #pkts compressed: 0, #pkts decompressed: 0
    #pkts not compressed: 0, #pkts compr. failed: 0
    #pkts not decompressed: 0, #pkts decompress failed: 0
    #send errors 0, #recv errors 0

     local crypto endpt.: 10.10.10.100, remote crypto endpt.: 10.20.20.6
     path mtu 1500, ip mtu 1500, ip mtu idb Ethernet0/2
     current outbound spi: 0x85094974(2231978356)
     PFS (Y/N): N, DH group: none

     inbound esp sas:
      spi: 0xFA967B19(4204165913)
        transform: esp-aes esp-md5-hmac ,
        in use settings ={Tunnel, }
        conn id: 120, flow_id: SW:120, sibling_flags 80000040, crypto map: Tunnel1020-head-0
        sa timing: remaining key lifetime (k/sec): (4272184/3516)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     inbound ah sas:

     inbound pcp sas:

     outbound esp sas:
      spi: 0x85094974(2231978356)
        transform: esp-aes esp-md5-hmac ,
        in use settings ={Tunnel, }
        conn id: 119, flow_id: SW:119, sibling_flags 80000040, crypto map: Tunnel1020-head-0
        sa timing: remaining key lifetime (k/sec): (4272183/3516)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     outbound ah sas:

     outbound pcp sas:
```
#### 2. Настроите DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги.  

Создадим на R15 новый профиль IPSEC 
```
crypto ipsec transform-set DMVPN esp-aes esp-md5-hmac 
 mode transport
crypto ipsec profile DMVPN_PROF
 set transform-set DMVPN 
 set ikev2-profile PROFILE1
```
R27 и R28 настраиваем как в Москве, со второй фазой для DMVPN
```
crypto ikev2 proposal PROMSK 
 encryption aes-cbc-128
 integrity md5
 group 2
crypto ikev2 policy IKEV2 
 proposal PROMSK
crypto ikev2 profile PROFILE1
 match address local interface Loopback0
 match identity remote address 0.0.0.0 
 authentication remote rsa-sig
 authentication local rsa-sig
 pki trustpoint VPN
crypto ipsec transform-set DMVPN esp-aes esp-md5-hmac 
 mode transport
crypto ipsec profile DMVPN_PROF
 set transform-set DMVPN 
 set ikev2-profile PROFILE1
```

Проверим на R27
```
R27#show crypto ikev2 sa
 IPv4 Crypto IKEv2  SA 

Tunnel-id Local                 Remote                fvrf/ivrf            Status 
2         10.35.0.1/500         10.10.10.100/500      none/none            READY  
      Encr: AES-CBC, keysize: 128, Hash: MD596, DH Grp:2, Auth sign: RSA, Auth verify: RSA
      Life/Active Time: 86400/57365 sec

 IPv6 Crypto IKEv2  SA 

R27#show crypto ipsec sa

interface: Tunnel1030
    Crypto map tag: Tunnel1030-head-0, local addr 10.35.0.1

   protected vrf: (none)
   local  ident (addr/mask/prot/port): (10.35.0.1/255.255.255.255/47/0)
   remote ident (addr/mask/prot/port): (10.10.10.100/255.255.255.255/47/0)
   current_peer 10.10.10.100 port 500
     PERMIT, flags={origin_is_acl,}
    #pkts encaps: 680, #pkts encrypt: 680, #pkts digest: 680
    #pkts decaps: 680, #pkts decrypt: 680, #pkts verify: 680
    #pkts compressed: 0, #pkts decompressed: 0
    #pkts not compressed: 0, #pkts compr. failed: 0
    #pkts not decompressed: 0, #pkts decompress failed: 0
    #send errors 0, #recv errors 0

     local crypto endpt.: 10.35.0.1, remote crypto endpt.: 10.10.10.100
     path mtu 1500, ip mtu 1500, ip mtu idb (none)
     current outbound spi: 0x31D6F09B(836169883)
     PFS (Y/N): N, DH group: none

     inbound esp sas:
      spi: 0x991A1A79(2568624761)
        transform: esp-aes esp-md5-hmac ,
        in use settings ={Transport, }
        conn id: 36, flow_id: SW:36, sibling_flags 80000000, crypto map: Tunnel1030-head-0
        sa timing: remaining key lifetime (k/sec): (4333222/1231)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     inbound ah sas:

     inbound pcp sas:

     outbound esp sas:
      spi: 0x31D6F09B(836169883)
        transform: esp-aes esp-md5-hmac ,
        in use settings ={Transport, }
        conn id: 35, flow_id: SW:35, sibling_flags 80000000, crypto map: Tunnel1030-head-0
        sa timing: remaining key lifetime (k/sec): (4333223/1231)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)
          
     outbound ah sas:

     outbound pcp sas:
R27#
```
Пинг и Москвы
```
VPC#ping 172.10.35.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.10.35.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/7 ms
VPC#
```

Посмотрим все фазы на R15
```
R15#show crypto ikev2 sa
 IPv4 Crypto IKEv2  SA 

Tunnel-id Local                 Remote                fvrf/ivrf            Status 
1         10.10.10.100/500      10.20.20.6/500        none/none            READY  
      Encr: AES-CBC, keysize: 128, Hash: MD596, DH Grp:2, Auth sign: RSA, Auth verify: RSA
      Life/Active Time: 86400/21 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status 
4         10.10.10.100/500      10.30.30.30/500       none/none            READY  
      Encr: AES-CBC, keysize: 128, Hash: MD596, DH Grp:2, Auth sign: RSA, Auth verify: RSA
      Life/Active Time: 86400/56539 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status 
3         10.10.10.100/500      10.35.0.1/500         none/none            READY  
      Encr: AES-CBC, keysize: 128, Hash: MD596, DH Grp:2, Auth sign: RSA, Auth verify: RSA
      Life/Active Time: 86400/56966 sec

 IPv6 Crypto IKEv2  SA 

R15#show crypto ipsec sa

interface: Tunnel1020
    Crypto map tag: Tunnel1020-head-0, local addr 10.10.10.100

   protected vrf: (none)
   local  ident (addr/mask/prot/port): (10.10.10.100/255.255.255.255/47/0)
   remote ident (addr/mask/prot/port): (10.20.20.6/255.255.255.255/47/0)
   current_peer 10.20.20.6 port 500
     PERMIT, flags={origin_is_acl,}
    #pkts encaps: 223, #pkts encrypt: 223, #pkts digest: 223
    #pkts decaps: 211, #pkts decrypt: 211, #pkts verify: 211
    #pkts compressed: 0, #pkts decompressed: 0
    #pkts not compressed: 0, #pkts compr. failed: 0
    #pkts not decompressed: 0, #pkts decompress failed: 0
    #send errors 0, #recv errors 0

     local crypto endpt.: 10.10.10.100, remote crypto endpt.: 10.20.20.6
     path mtu 1500, ip mtu 1500, ip mtu idb Ethernet0/2
     current outbound spi: 0x85094974(2231978356)
     PFS (Y/N): N, DH group: none

     inbound esp sas:
      spi: 0xFA967B19(4204165913)
        transform: esp-aes esp-md5-hmac ,
        in use settings ={Tunnel, }
        conn id: 120, flow_id: SW:120, sibling_flags 80000040, crypto map: Tunnel1020-head-0
        sa timing: remaining key lifetime (k/sec): (4272184/3516)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     inbound ah sas:

     inbound pcp sas:

     outbound esp sas:
      spi: 0x85094974(2231978356)
        transform: esp-aes esp-md5-hmac ,
        in use settings ={Tunnel, }
        conn id: 119, flow_id: SW:119, sibling_flags 80000040, crypto map: Tunnel1020-head-0
        sa timing: remaining key lifetime (k/sec): (4272183/3516)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     outbound ah sas:

     outbound pcp sas:

interface: Tunnel1030
    Crypto map tag: Tunnel1030-head-0, local addr 10.10.10.100

   protected vrf: (none)
   local  ident (addr/mask/prot/port): (10.10.10.100/255.255.255.255/47/0)
   remote ident (addr/mask/prot/port): (10.35.0.1/255.255.255.255/47/0)
   current_peer 10.35.0.1 port 500
     PERMIT, flags={origin_is_acl,}
    #pkts encaps: 677, #pkts encrypt: 677, #pkts digest: 677
    #pkts decaps: 677, #pkts decrypt: 677, #pkts verify: 677
    #pkts compressed: 0, #pkts decompressed: 0
    #pkts not compressed: 0, #pkts compr. failed: 0
    #pkts not decompressed: 0, #pkts decompress failed: 0
    #send errors 0, #recv errors 0

     local crypto endpt.: 10.10.10.100, remote crypto endpt.: 10.35.0.1
     path mtu 1500, ip mtu 1500, ip mtu idb (none)
     current outbound spi: 0x991A1A79(2568624761)
     PFS (Y/N): N, DH group: none

     inbound esp sas:
      spi: 0x31D6F09B(836169883)
        transform: esp-aes esp-md5-hmac ,
        in use settings ={Transport, }
        conn id: 115, flow_id: SW:115, sibling_flags 80000000, crypto map: Tunnel1030-head-0
        sa timing: remaining key lifetime (k/sec): (4232876/1576)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     inbound ah sas:

     inbound pcp sas:

     outbound esp sas:
      spi: 0x991A1A79(2568624761)
        transform: esp-aes esp-md5-hmac ,
        in use settings ={Transport, }
        conn id: 116, flow_id: SW:116, sibling_flags 80000000, crypto map: Tunnel1030-head-0
        sa timing: remaining key lifetime (k/sec): (4232876/1576)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     outbound ah sas:

     outbound pcp sas:

   protected vrf: (none)
   local  ident (addr/mask/prot/port): (10.10.10.100/255.255.255.255/47/0)
   remote ident (addr/mask/prot/port): (10.30.30.30/255.255.255.255/47/0)
   current_peer 10.30.30.30 port 500
     PERMIT, flags={origin_is_acl,}
    #pkts encaps: 656, #pkts encrypt: 656, #pkts digest: 656
    #pkts decaps: 61579, #pkts decrypt: 61579, #pkts verify: 61579
    #pkts compressed: 0, #pkts decompressed: 0
    #pkts not compressed: 0, #pkts compr. failed: 0
    #pkts not decompressed: 0, #pkts decompress failed: 0
    #send errors 0, #recv errors 0

     local crypto endpt.: 10.10.10.100, remote crypto endpt.: 10.30.30.30
     path mtu 1500, ip mtu 1500, ip mtu idb (none)
     current outbound spi: 0x5A104C1F(1511017503)
     PFS (Y/N): N, DH group: none

     inbound esp sas:
      spi: 0x8A68AB91(2322115473)
        transform: esp-aes esp-md5-hmac ,
        in use settings ={Transport, }
        conn id: 117, flow_id: SW:117, sibling_flags 80000000, crypto map: Tunnel1030-head-0
        sa timing: remaining key lifetime (k/sec): (4307125/2084)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     inbound ah sas:

     inbound pcp sas:

     outbound esp sas:
      spi: 0x5A104C1F(1511017503)
        transform: esp-aes esp-md5-hmac ,
        in use settings ={Transport, }
        conn id: 118, flow_id: SW:118, sibling_flags 80000000, crypto map: Tunnel1030-head-0
        sa timing: remaining key lifetime (k/sec): (4307341/2084)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     outbound ah sas:

     outbound pcp sas:
```
