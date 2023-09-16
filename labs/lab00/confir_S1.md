> R1#show run     
Building configuration...  
Current configuration : 3572 bytes    
!  
! Last configuration change at 19:06:25 MSK Fri Sep 8 2023  
!   
version 15.9  
service timestamps debug datetime msec  
service timestamps log datetime msec  
service password-encryption  
!  
hostname R1  
!  
boot-start-marker  
boot-end-marker  
!  
!  
enable secret 9 $9$oYvdB/APC0D97D$UMRrTAlPJaJH2HTcyl6pOE1ayLvSCSgbTucFmIiazEk  
!  
no aaa new-model  
!   
!  
!  
clock timezone MSK 3 0  
mmi polling-interval 60  
no mmi auto-configure  
no mmi pvc  
mmi snmp-timeout 180  
!  
!  
!  
!  
!  
!  
!  
!  
!  
!  
!  
no ip domain lookup  
ip cef  
ipv6 unicast-routing  
ipv6 cef  
!  
multilink bundle-name authenticated  
!  
!  
password encryption aes  
!  
redundancy  
!  
!  
!  
!  
!  
!  
interface GigabitEthernet0/0  
 no ip address  
 duplex auto  
 speed auto  
 media-type rj45  
!
interface GigabitEthernet0/0.3  
 description Management  
 encapsulation dot1Q 3  
 ip address 192.168.3.1 255.255.255.0  
 ipv6 address FC0::3:1/118  
!  
interface GigabitEthernet0/0.4  
 description Operations  
 encapsulation dot1Q 4  
 ip address 192.168.4.1 255.255.255.0  
 ipv6 address FC0::4:1/118  
!  
interface GigabitEthernet0/0.8  
 description Native  
 encapsulation dot1Q 8 native    
!  
interface GigabitEthernet0/1  
 no ip address  
 shutdown  
 duplex auto  
 speed auto  
 media-type rj45   
!  
interface GigabitEthernet0/2  
 no ip address  
 shutdown  
 duplex auto  
 speed auto  
 media-type rj45  
!  
interface GigabitEthernet0/3  
 no ip address  
 shutdown  
 duplex auto  
 speed auto  
 media-type rj45  
!  
ip forward-protocol nd  
!  
!  
no ip http server  
!  
ipv6 ioam timestamp  
!  
!  
!  
control-plane  
!  
banner motd ^CThis is a secure system. Authorized Access Only!^C  
!  
line con 0  
 exec-timeout 0 0  
 password 7 104D000A0618  
 logging synchronous  
 login  
line aux 0  
line vty 0 4  
 password 7 121A0C041104  
 logging synchronous  
 login  
 transport input none  
!  
no scheduler allocate  
!  
end  
