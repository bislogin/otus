### Часть 1: Построение сети и настройка основных параметров устройства

#### Шаг 1: Подключение сети, как показано в топологии.
![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv4/%D0%91%D0%B5%D0%B7%D1%8B%D0%BC%D1%8F%D0%BD%D0%BD%D1%8B%D0%B9.png)

#### Шаг 2: Настройка основных параметров маршрутизатора.

Router#conf t  
Router(config)#hostname R1  
R1(config)#no ip domain lookup  
R1(config)#enable secret class   
R1(config)#line console 0  
R1(config-line)#password cisco  
R1(config)#line vty 0 4  
R1(config-line)#password cisco  
R1(config-line)#login  
R1(config)#service password-encryption   
