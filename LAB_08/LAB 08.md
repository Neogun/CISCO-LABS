# Лабраторная работа - Настройка DHCPv6 

**Выполнил Давыдкин Д.Г.**

### Топология

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_08/Images/Topology.jpg)


------------


### Таблица адресации

| Устройство  | Интерфейс  | IPv6-адрес  |
| ------------ | ------------ | ------------ |
| R1  | G0/0/0  | 2001: db8: acad :2 ::1/64  |
| R1  | G0/0/0  | fe80::1  |
| R1  |G0/0/1   | 2001: db8: acad: 1:: 1/64  |
|R1   | G0/0/1  | fe80::1  |
| R2  |G0/0/0   | 2001: db8: acad: 2::2/64  |
|R2   |G0/0/0   | fe80::2  |
| R2  | G0/0/1  | 2001: db8: acad: 3::1/64  |
| R2  | G0/0/1  | fe80::1  |
|PC-A   |NIC   | DHCP  |
| PC-B  | NIC  | DHCP  |
------------
### Задачи

Часть 1. Создание сети и настройка основных параметров устройства  
Часть 2. Проверка назначения адреса SLAAC от R1  
Часть 3. Настройка и проверка сервера DHCPv6 без гражданства на R1  
Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1  
Часть 5. Настройка и проверка DHCPv6 Relay на R2  


------------

### Часть 1. Создание сети и настройка основных параметров устройства

*Часть 1. Создание сети и настройка основных параметров устройства*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_08/Images/Network%20PT.jpg)

*Шаг 2. Настройте базовые параметры каждого коммутатора. *

**Настройка S1**

Switch>  
Switch>en  
Switch#clock set 11:55:00 june 19 2022  
Switch#conf t  
Switch(config)#hostname S1  
S1(config)#no ip domain lookup  
S1(config)#enable secret class  
S1(config)#service password-encryption  
S1(config)#banner motd ###ENTRY CLOSED!!!###  
S1(config)#line con 0  
S1(config-line)#logging synchronous  
S1(config-line)#password cisco  
S1(config-line)#login  
S1(config-line)#line vty 0 15  
S1(config-line)#logging synchronous  
S1(config-line)#password cisco  
S1(config-line)#login  
S1(config-line)#int range fa0/1-4, fa0/7-24, gi0/1-2, vlan 1  
S1(config-if-range)#shu  
S1(config-if-range)#do wr  
Building configuration...  
[OK]  

**Настройка S2** 
Switch>  
Switch>en  
Switch#clock set 12:00:00 june 19 2022  
Switch#conf t  
Switch(config)#hostname S2  
S2(config)#no ip domain lookup  
S2(config)#enable secret class  
S2(config)#service password-encryption  
S2(config)#banner motd ###ENTRY CLOSED!!!###  
S2(config)#line con 0  
S2(config-line)#logging synchronous  
S2(config-line)#password cisco  
S2(config-line)#login  
S2(config-line)#line vty 0 15  
S2(config-line)#logging synchronous  
S2(config-line)#password cisco  
S2(config-line)#login  
S2(config-line)#int range fa0/1-4, fa0/6-17, fa0/19-24,gi0/1-2, vlan 1  
S2(config-if-range)#shu  
S2(config-if-range)#do wr  
Building configuration...  
[OK]  

*Шаг 3. Произведите базовую настройку маршрутизаторов.*

**Настройка R1**

Router>en  
Router#clock set 12:05:00 june 19 2022  
Router#conf t  
Router(config)#hostname R1  
R1(config)#no ip domain lookup  
R1(config)#enable secret class  
R1(config)#service password-encryption  
R1(config)#banner motd ###ENTRY CLOSED!!!#  
R1(config)#ipv6 unicast-routing   
R1(config)#line con 0  
R1(config-line)#logging synchronous  
R1(config-line)#password cisco  
R1(config-line)#login  
R1(config)#line vty 0 15  
R1(config-line)#logging synchronous  
R1(config-line)#password cisco  
R1(config-line)#login  
R1(config-line)#int range g0/0/0-1  
R1(config-if)#no shu  
R1(config-if)#do wr  

**Настройка R2**

Router>en  
Router#clock set 12:05:00 june 19 2022  
Router#conf t  
Router(config)#hostname R1  
R2(config)#no ip domain lookup  
R2(config)#enable secret class  
R2(config)#service password-encryption  
R2(config)#banner motd ###ENTRY CLOSED!!!#  
R2(config)#ipv6 unicast-routing   
R2(config)#line con 0  
R2(config-line)#logging synchronous  
R2(config-line)#password cisco  
R2(config-line)#login  
R2(config)#line vty 0 15  
R2(config-line)#logging synchronous  
R2(config-line)#password cisco  
R2(config-line)#login  
R2(config-line)#int range g0/0/0-1  
R2(config-if)#no shu  
R2(config-if)#do wr  

*Шаг 4. Настройка интерфейсов и маршрутизации для обоих маршрутизаторов.*

a.	Настройте интерфейсы G0/0/0 и G0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше.

**Настройка R1**

R1(config)#int gig0/0/0  
R1(config-if)#ipv6 address 2001: db8: acad: 2::1/64  
R1(config-if)#ipv6 address fe80::1 link-local  
R1(config-if)#int gig0/0/1  
R1(config-if)#ipv6 address 2001:db8: acad: 1::1/64  
R1(config-if)#ipv6 address fe80::1 link-local  

**Настройка R2**

R2(config)#int gig0/0/0  
R2(config-if)#ipv6 address 2001: db8: acad: 2::2/64  
R2(config-if)#ipv6 address fe80::2 link-local  
R2(config-if)#int gig0/0/1  
R2(config-if)#ipv6 address 2001: db8: acad: 3::1/64  
R2(config-if)#ipv6 address fe80::1 link-local  

b.	Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес G0/0/0 на другом маршрутизаторе.

**Настройка R1**

ipv6 route ::/0 2001:db8: acad: 2::2
R2(config)#do wr  
Building configuration...  
[OK]  

**Настройка R2**

R2(config)#ipv6 route ::/0 2001: db8: acad: 2::1  
R2(config)#do wr  
Building configuration...  
[OK]  

c.	Убедитесь, что маршрутизация работает с помощью пинга адреса G0/0/1 R2 из R1

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_08/Images/PING%20R1%20to%20G0_0_1%20R2.jpg)


------------

### Часть 2. Проверка назначения адреса SLAAC от R1

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_08/Images/IPv6%20PC-A.jpg)

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_08/Images/ipconfig%20PC-A.jpg)


Откуда взялась часть адреса с идентификатором хоста? **С помощью технолгии EUI-64.**

### Часть 3. Настройка и проверка сервера DHCPv6 на R1

*Шаг 1. Более подробно изучите конфигурацию PC-A.*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_08/Images/ipconfig%20PC-A%20v2.jpg)

*Шаг 2. Настройте R1 для предоставления DHCPv6 без состояния для PC-A.*

a.	Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS. В составе этого пула назначьте адрес DNS-сервера как 2001:db8:acad: :254, а имя домена — как stateless.com.

R1(config)# ipv6 dhcp pool R1-STATELESS  
R1(config-dhcp)# dns-server 2001:db8: acad::254  
R1(config-dhcp)# domain-name STATELESS.com  

b.	Настройте интерфейс G0/0/1 на R1, чтобы предоставить флаг конфигурации OTHER для локальной сети R1 и укажите только что созданный пул DHCP в качестве ресурса DHCP для этого интерфейса.

R1(config)# interface g0/0/1  
R1(config-if)# ipv6 nd other-config-flag   
R1(config-if)# ipv6 dhcp server R1-STATELESS  
R1(config-if)# do wr  

e.	Проверьте вывод ipconfig /all и обратите внимание на изменения.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_08/Images/ipconfig%20PC-A%20v2.jpg)

f.	Тестирование подключения с помощью пинга IP-адреса интерфейса G0/1 R2.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_08/Images/PING%20PC-A%20to%20R2%20Gig0_0_1.jpg)


------------


### Часть 4. Настройка сервера DHCPv6 с сохранением состояния на R1

a.	Создайте пул DHCPv6 на R1 для сети 2001: db8: acad: 3: aaa::/80. Это предоставит адреса локальной сети, подключенной к интерфейсу G0/0/1 на R2. В составе пула задайте DNS-сервер 2001: db8: acad: :254 и задайте доменное имя STATEFUL.com.

R1(config)# ipv6 dhcp pool R2-STATEFUL  
R1(config-dhcp)# address prefix 2001: db8: acad: 3: aaa::/80  
R1(config-dhcp)# dns-server 2001: db8: acad::254  
R1(config-dhcp)# domain-name STATEFUL.com  

b.	Назначьте только что созданный пул DHCPv6 интерфейсу g0/0/0 на R1.

R1(config)# interface g0/0/0  
R1(config-if)# ipv6 dhcp server R2-STATEFUL  

### Часть 5. Настройка и проверка ретрансляции DHCPv6 на R2.

*Шаг 1. Включите PC-B и проверьте адрес SLAAC, который он генерирует.*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_08/Images/ipconfig%20PC-B.jpg)

*Шаг 2. Настройте R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1.*

R2 (конфигурация) # интерфейс g0/0/1
R2(config-if)# ipv6 nd managed-config-flag
R2(config-if)# ipv6 dhcp relay destination 2001: db8: acad: 2::1 g0/0/0\

**!~К сожалению в Packet Tracer не поддерживается функция ipv6 dhcp relay, поэтому полностью выполнить лабу не получилось.**


c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_08/Images/PING%20PC-B%20to%20R1%20GI0_0_1.jpg)
