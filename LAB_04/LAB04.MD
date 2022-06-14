# Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах 
**Выполнил Давыдкин Д.Г.**

### Топология

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


c)
R1(config)#int g0/0/0

R1(config-if)#ipv6 address fe80::1:1 link-local 

R1(config)#int g0/0/1

ipv6 address fe80::a:1 link-local 


R1(config)#int g0/0/0

R1(config-if)#ipv6 address fe80::1 link-local 

R1(config-if)#int g0/0/1

R1(config-if)#ipv6 address fe80::1 link-local 

d)

*Какие группы многоадресной рассылки назначены интерфейсу G0/0?*
 Joined group address(es):
    FF02::1
    FF02::1:FF00:1
	
*Шаг 2. Активируйте IPv6-маршрутизацию на R1.*

a) Адрес не назначен.

b) R1(config)#ipv6 unicast-routing 

c) Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?** Да, получил.**

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

b)
show ipv6 interface vlan1


Шаг 4. Назначьте компьютерам статические IPv6-адреса.

### Часть 3. Проверка сквозного подключения

