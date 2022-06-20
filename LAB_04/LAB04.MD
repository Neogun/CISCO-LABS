# Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах 
**Выполнил Давыдкин Д.Г.**

### Топология

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/topology.jpg)

### Таблица адресации

|  Устройство | Интерфейс  | IPv6-адрес  | Длина префикса  | Шлюз по умолчанию  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| R1 | G0/0/0 G0/0/1  | 2001:db: acad: a::1 ; 2001:db8:acad:1::1  | 64  |-   |
|S1 | VLAN 1  | 2001:db8:acad:1::b  |64   | -  |
| PC-A  | NIC  | 2001:db8:acad:1::3  |64   | fe80::1  |
| PC-B  | NIC  | 2001:db8:acad : a::3  | 64  |  fe80::1 |

### Задачи
Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора
Часть 2. Ручная настройка IPv6-адресов
Часть 3. Проверка сквозного соединения

### Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

*Шаг 1. Настройте маршрутизатор.*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/cisco%20packet.jpg)

Router>

Router>en

Router#clock set 22:02:00 june 14 2022

Router#conf t

Router(config)#hostname R1

R1(config)#banner motd #Entry closed!!!#

R1(config)#no ip domain-lookup

R1(config)#enable secret class

R1(config)#line con 0

R1(config-line)#logging synchronous 

R1(config-line)#password cisco 

R1(config-line)#login

R1(config-line)#line vty 0 15

R1(config-line)#logging synchronous 

R1(config-line)#password cisco 

R1(config-line)#login

R1(config)#int gig 0/0/0

R1(config-if)#no shu

R1(config-if)#int gig 0/0/1

R1(config-if)# do wr

*Шаг 2. Настройте коммутатор.*

Switch>en

Switch#clock set 22:10:00 june 14 2022

Switch#conf t

Switch(config)#hostname S1

S1(config)#no ip domain-lookup 

S1(config)#enable secret class

S1(config)#banner motd #Entry closed!!!#

S1(config)#line con 0

S1(config-line)#logging synchronous 

S1(config-line)#password cisco

S1(config-line)#login

S1(config-line)#line vty 0 15

S1(config-line)#logging synchronous

S1(config-line)#password cisco

S1(config-line)#login

S1(config-line)#do wr


### Часть 2. Ручная настройка IPv6-адресов

*Шаг 1. Назначьте IPv6-адреса интерфейсам Ethernet на R1.*

a-b)

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/show%20ipv6%20v1.jpg)

c)

R1(config)#int g0/0/0

R1(config-if)#ipv6 address fe80::1 link-local 

R1(config-if)#int g0/0/1

R1(config-if)#ipv6 address fe80::1 link-local 

d)

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/show%20ipv6%20link%20local.jpg)

*Какие группы многоадресной рассылки назначены интерфейсу G0/0?*  
 Joined group address(es):  
    FF02::1  
    FF02::1:FF00:1  
	
*Шаг 2. Активируйте IPv6-маршрутизацию на R1.*

a) Адрес не назначен.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/ipconfig%20PC-B.jpg)

b) R1(config)#ipv6 unicast-routing 

c) Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1? **Да, получил.** 
По такому принципу работает SLAAC, маршрутизатор начинает рассылать icmpv6 запросы.

*Шаг 3. Назначьте IPv6-адреса интерфейсу управления (SVI) на S1.*
a)

S1# conf t  
S1(config)# sdm prefer dual-ipv4-and-ipv6 default  
S1(config)# end  
S1# reload  

S1(config)#int vlan1  
S1(config-if)#ipv6 address 2001:db8:acad:1::b/64  
S1(config-if)#ipv6 address fe80::1 link-local   
S1(config-if)#no shu  
  
![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/show%20ip%20vlan%201.jpg)

b)
**show ipv6 interface vlan1**


Шаг 4. Назначьте компьютерам статические IPv6-адреса.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/Static%20%20PC-A.jpg)

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/Static%20PC-B.jpg)

### Часть 3. Проверка сквозного подключения

С PC-A отправьте эхо-запрос на FE80::1. Это локальный адрес канала, назначенный G0/1 на R1.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/Ping%20PC-A%20to%20R1%20G001.jpg)


Отправьте эхо-запрос на интерфейс управления S1 с PC-A.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/Ping%20PC-A%20to%20S1.jpg)


Введите команду tracert на PC-A, чтобы проверить наличие сквозного подключения к PC-B.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/tracert%20PC-A%20to%20PC-B.jpg)


С PC-B отправьте эхо-запрос на PC-A.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/PING%20PC-B%20to%20PC-A.jpg)


С PC-B отправьте эхо-запрос на локальный адрес канала G0/0 на R1.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/Images/PC-B%20ping%20to%20R1%20G000.jpg)


### Вопросы для повторения
1.	Почему обоим интерфейсам Ethernet на R1 можно назначить один и тот же локальный адрес канала — FE80::1? **Потому что данный адрес используется только в одном сегменте сети между двумя узлами. Поэтому в каждой паре узлов можно назначать один и тот же адрес.**
2.	Какой идентификатор подсети в индивидуальном IPv6-адресе 2001:db8:acad::aaaa:1234/64? **2001:0db8:acad:0000:0000:0000:0000:0000**
