### Часть 1: Построение сети и настройка основных параметров устройства

#### Шаг 1: Подключение сети, как показано в топологии.
![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv4/%D0%91%D0%B5%D0%B7%D1%8B%D0%BC%D1%8F%D0%BD%D0%BD%D1%8B%D0%B9.png)

#### Шаг 2: Настройка основных параметров маршрутизатора.

a. Console into the router and enable privileged EXEC mode.
> Router>en  
> Router#

b. Enter configuration mode.
> Router#conf t

c. Assign a device name to the router.
> Router(config)#hostname R1

d.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names.  
> R1(config)#no ip domain lookup

e.	Assign class as the privileged EXEC encrypted password.  
> R1(config)#enable secret class

f.	Assign cisco as the console password and enable login.  
> R1(config)#line console 0  
> R1(config-line)#password cisco  
> R1(config-line)#login    
> R1(config-line)#logging synchronous

g.	Assign cisco as the VTY password and enable login.  
> R1(config)#line vty 0 4  
> R1(config-line)#password cisco  
> R1(config-line)#login  
> R1(config-line)#logging synchronous

h.	Encrypt the plaintext passwords.
> R1(config)#service password-encryption

i.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.
> R1(config)#Banner motd "This is a secure system. Authorized Access Only!"

j.	Save the running configuration to the startup configuration file.
> R1(config)# do wr

k.	Set the clock on the router.
> R1(config)#clock timezone MSK +3








