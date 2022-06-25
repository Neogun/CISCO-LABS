# Лабораторная работа - Реализация DHCPv4.

**Выполнил Давыдкин Д.Г.**


------------

### Топология

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/Topology.jpg)



------------

### Таблица адресации

| Устройство  | Интерфейс  |  IP-адрес |Маска подсети   | Шлюз по умолчанию  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
|R1   | G0/0/0  | 10.0.0.1 |255.255.255.252   | -  |
| R1  | G0/0/1  |  - |  - | -  |
| R1  | G0/0/1.100  | 192.168.1.1  | 255.255.255.192    |-   |
| R1  | G0/0/1.200  | 192.168.1.65  | 255.255.255.224   |  - |
|R1   | G0/0/1.1000   |-  | -  |  - |
|R2   |  G0/0/0 | 10.0.0.2  |255.255.255.252   |-   |
| R2  | G0/0/1  |  192.168.1.97 | 255.255.255.240    | -  |
| S1  | VLAN 200  | 192.168.1.66 |255.255.255.224    | 192.168.1.96  |
|S2   | VLAN 1  |   |   |   |
| PC-A  |  NIC |DHCP   | DHCP | DHCP  |
| PC-B  | NIC  | DHCP  |DHCP   |  DHCP |


### Таблица VLAN

| VLAN  | Имя  | Имя  |
| ------------ | ------------ | ------------ |
| 1  | Нет  | S2: F0/18  |
|100   |Клиенты   |S2: F0/18   |
| 200  | Управление  |S1: VLAN 200     |
| 999  | Parking_Lot  |S1: F0/1-4, F0/7-24, G0/1-2   |
| 1000  |Собственная   | —  |

------------

### Задачи

Часть 1. Создание сети и настройка основных параметров устройства
Часть 2. Настройка и проверка двух серверов DHCPv4 на R1
Часть 3. Настройка и проверка DHCP-ретрансляции на R2



------------
### Часть 1. Создание сети и настройка основных параметров устройства


*Шаг 1.	Создание схемы адресации*

Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:
a.	Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).
Подсеть A
Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100 . 

**Network 192.168.1.0  
Mask 255.255.255.192  
Broadcast 192.168.1.63  
G0/0/1.100 192.168.1.1**  

b.	Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1). 
Подсеть B:
Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200. Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию.

**Network 192.168.1.64  
Mask 	255.255.255.224  
Broadcast 192.168.1.95  
G0/0/1.200 192.168.1.65   
S1 VLAN 200 192.168.1.66**  

c.	Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).
Подсеть C:
Запишите первый IP-адрес в таблице адресации для R2 G0/0/1.

**Network 192.168.1.96  
Mask 255.255.255.240  
Broadcast 192.168.1.111  
R2 G0/0/1 192.168.1.97**  

*Шаг 2. Создайте сеть согласно топологии.*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/Network%20PT.jpg)

*Шаг 3. Произведите базовую настройку маршрутизаторов.*

**Настройка R1 **

Router>en  
Router#clock set 14:05:00 june 23 2022  
Router#conf t  
Router(config)#hostname R1  
R1(config)#no ip domain-lookup  
R1(config)#enable secret class  
R1(config)#service password-encryption  
R1(config)#banner motd ###ENTRY CLOSED!!!#  
R1(config)#line con 0  
R1(config-line)#logging synchronous  
R1(config-line)#password cisco  
R1(config-line)#login  
R1(config)#line vty 0 15  
R1(config-line)#logging synchronous  
R1(config-line)#password cisco  
R1(config-line)#login  
R1(config-if)#do wr  

**Настройка R2**

Router>en  
Router#clock set 14:10:00 june 23 2022  
Router#conf t  
Router(config)#hostname R2  
R2(config)#no ip domain-lookup  
R2(config)#enable secret class   
R2(config)#service password-encryption  
R2(config)#banner motd ###ENTRY CLOSED!!!#  
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


*Шаг 4.	Настройка маршрутизации между сетями VLAN на маршрутизаторе R1*

a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.

R1(config)#int g0/0/1  
R1(config-if)#no shu  


b.	Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

R1(config-if)#int g0/0/1.100  
R1(config-subif)#ip address 192.168.1.1 255.255.255.192  
R1(config-subif)#encapsulation dot1Q 100  
R1(config-subif)#description MGMT  

R1(config)#int g0/0/1.200  
R1(config-subif)#ip address 192.168.1.65 255.255.255.224  
R1(config-subif)#encapsulation dot1Q 200  
R1(config-subif)#description MGMT  

R1(config-subif)#int g0/0/1.1000  
R1(config-subif)#description TRUNK  

c.	Убедитесь, что вспомогательные интерфейсы работают.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/show%20IP%20Interface%20brief.jpg)

Шаг 5.	Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов

a.	Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.

R2(config)#int g0/0/1  
R2(config-if)#ip address 192.168.1.97 255.255.255.240  

b.	Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.

R2(config)#int g0/0/0  
R2(config-if)#ip address 10.0.0.2 255.255.255.252  

R1(config)#int g0/0/0  
R1(config-if)#ip address 10.0.0.1 255.255.255.252  

c.	Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.

R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2  

R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1  

d.	Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.

![Image]()

*Шаг 6. Настройте базовые параметры каждого коммутатора.*

**Настройка S1**

Switch>  
Switch>en  
Switch#clock set 15:10:00 june 23 2022  
Switch#conf t  
Switch(config)#hostname S1  
S1(config)#no ip domain-lookup  
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
S1(config-if-range)#do wr  
Building configuration...  
[OK]  

**Настройка S2**

Switch>  
Switch>en  
Switch#clock set 15:15:00 june 23 2022  
Switch#conf t  
Switch(config)#hostname S1  
S2(config)#no ip domain-lookup  
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
S2(config-if-range)#do wr  
Building configuration...  
[OK]  


*Шаг 7.	Создайте сети VLAN на коммутаторе S1.*

a.	Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.

S1(config)#vlan 100  
S1(config-vlan)#name Clients  
S1(config-vlan)#vlan 200   
S1(config-vlan)#name MGMT  
S1(config-vlan)#vlan 999  
S1(config-vlan)#name Parking_Lot  
S1(config-vlan)#vlan 1000  
S1(config-vlan)#name Own  

b.	Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.

S1(config)#int vlan 200  
S1(config-if)#ip address 192.168.1.66 255.255.255.224    
S1(config-if)#no shu    
S1(config)#ip default-gateway 192.168.1.65  

c.	Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2

S2(config)#int vlan 1
S2(config-if)#ip address 192.168.1.66  255.255.255.224
S2(config-if)#no shu
 
d.	Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.

S1(config)#int r f0/1-4, f0/7-24, g0/1-2  
S1(config-if-range)#switchport mode access   
S1(config-if-range)#switchport access vlan 999  
S1(config-if-range)#switchport nonegotiate   
S1(config-if-range)#shu  

Шаг 8.	Назначьте сети VLAN соответствующим интерфейсам коммутатора.

a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

S1(config)#int fa0/6  
S1(config-if)#switchport mode access   
S1(config-if)#switchport access vlan 100  

S2(config)#int fa0/18  
S2(config-if)#switchport mode access   
S2(config-if)#switchport access vlan 1  


b.	Убедитесь, что VLAN назначены на правильные интерфейсы.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/show%20vlan%20brief%20S1.jpg)

Вопрос:
Почему интерфейс F0/5 указан в VLAN 1?

**По умолчанию все порты находятся во VLAN 1.**

*Шаг 9.	Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.*

a.	Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.
b.	В рамках конфигурации транка  установите для native  VLAN значение 1000.
c.	В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.
d.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

**S1(config)#int fa0/5  
S1(config-if)#switchport mode trunk   
S1(config-if)#switchport trunk native vlan 1000  
S1(config-if)#switchport trunk allowed vlan 100,200,1000  
S1(config-if)#do wr**

e.	Проверьте состояние транка.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/show%20vlan%20brief%20check%20TRUNK.jpg) 

Порт fa0/5 пропал из таблицы VLAN. Так как native vlan не отображаются в ней.

Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?

**192.168.1.2**


------------



### Часть 2.	Настройка и проверка двух серверов DHCPv4 на R1

*Шаг 1.	Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A*

R1(config)#ip dhcp pool Clients  
R1(dhcp-config)#network 192.168.1.0 255.255.255.192  
R1(dhcp-config)#domain CCNA-lab.com  
R1(dhcp-config)#default-router 192.168.1.1  

R1(dhcp-config)#ip dhcp pool MGMT  
R1(dhcp-config)#network 192.168.1.64 255.255.255.224  
R1(dhcp-config)#default-router 192.168.1.65  
R1(dhcp-config)#domain CCNA-lab.com  

R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5  
R1(config)#ip dhcp excluded-address 192.168.1.65 192.168.1.69  

**!~В Cisco Packet Tracer отсутствует возможность настройки аренды с помощью команды lease.**

g.	Затем настройте второй пул DHCPv4, используя имя пула R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.

R1(config)#ip dhcp pool R2_Client_LAN  
R1(dhcp-config)#network 192.168.1.96 255.255.255.240  
R1(dhcp-config)#default-router 192.168.1.97  
R1(dhcp-config)#domain-name CCNA-lab.com  
R1(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101 

*Шаг 2.	Сохраните конфигурацию.*

R1(config)#do wr  
Building configuration...  
[OK]  

*Шаг 3.	Проверка конфигурации сервера DHCPv4*

a.	Чтобы просмотреть сведения о пуле, выполните команду show ip dhcp pool .

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/show%20ip%20dhcp%20pool.jpg)

b.	Выполните команду show ip dhcp bindings для проверки установленных назначений адресов DHCP.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/show%20ip%20dhcp%20binding.jpg)

**!~IP адрес отображается так как я ранее включил на PC-A автоматическую настройку.**

c.	Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.

**Не поддерживется в Cisco Packet Tracer.**

*Шаг 4.	Попытка получить IP-адрес от DHCP на PC-A*

![image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/PC-A%20DHCP%20Check.jpg)

c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.100

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/Ping%20PC-A%20to%20R1.jpg)


------------

### Часть 3.	Настройка и проверка DHCP-ретрансляции на R2

*Шаг 1.	Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1*

R2(config)#int g0/0/1  
R2(config-if)#ip helper-address 10.0.0.2  
R2(config-if)#do wr  
Building configuration...  
[OK]  

*Шаг 2.	Попытка получить IP-адрес от DHCP на PC-B*

a. Попытка получить IP-адрес от DHCP на PC-B

![image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/PC-B%20DHCP%20Check.jpg)

c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.

![image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/ping%20PC-B%20to%20R1.jpg)

d.	Выполните show ip dhcp binding для R1 для проверки назначений адресов в DHCP.

![image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_04/DHCP%20v4/Images/show%20ip%20dhcp%20binding%202.jpg)

e.	Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.

**Не поддерживется в Cisco Packet Tracer.**
