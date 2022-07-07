# Лабораторная работа. Настройка и проверка расширенных списков контроля доступа.

**Выполнил Давыдкин Д.Г.**


------------

### Топология

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_11/Images/Topology.jpg)



------------

### Таблица адресации

| Устройство  |Интерфейс   |  IP-адрес |Маска подсети    | Шлюз по умолчанию  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| R1  | G0/0/1  |  -- | --  | --  |
| R1  | G0/0/1.20  | 10.20.0.1  |255.255.255.0   |--   |
| R1  |G0/0/1.30   | 10.30.0.1  | 255.255.255.0  | --  |
| R1  |G0/0/1.40   | 10.40.0.1  | 255.255.255.0  |--   |
| R1  | G0/0/1.40  | 172.16.1.1  |--   |  -- |
| R1  | Loopback1  |172.16.1.1  | 255.255.255.0  | --  |
| R2  |G0/0/1   | 10.20.0.4  | 255.255.255.0  | --  |
|  S1 |VLAN 20   | 10.20.0.2  | 255.255.255.0  |10.20.0.1   |
| S2  | VLAN 20  | 10.20.0.3  |255.255.255.0   | 10.20.0.1  |
| PC-A  |NIC   | 10.30.0.10  | 255.255.255.0  | 10.30.0.1  |
|PC-B   | NIC  | 10.40.0.10  | 255.255.255.0  | 10.40.0.1  |


------------

### Таблица VLAN

| VLAN  |Имя   | Назначенный интерфейс  |
| ------------ | ------------ | ------------ |
| 20  | Management  | S2: F0/5  |
| 30 | Operations  | S1: F0/6  |
| 40  |Sales   | S2: F0/18  |
|999   | ParkingLot|S1: F0/2-4, F0/7-24, G0/1-2  __ S2: F0/2-4, F0/6-17, F0/19-24, G0/1-2|
|1000   | Собственная  |--   |


------------

### Задачи

Часть 1. Создание сети и настройка основных параметров устройства
Часть 2. Настройка и проверка списков расширенного контроля доступа


------------
### Часть 1. Создание сети и настройка основных параметров устройства

*Шаг 1. Создайте сеть согласно топологии.*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_11/Images/Network_PT.jpg)

*Шаг 2. Произведите базовую настройку маршрутизаторов.*

**Настройка R1 **

Router>en  
Router#clock set 12:05:00 june 19 2022  
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
Router#clock set 12:05:00 june 19 2022  
Router#conf t  
Router(config)#hostname R2 
R2(config)#no ip domain lookup  
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


------------


*Шаг 3. Настройте базовые параметры каждого коммутатора.*

**Настройка S1**

Switch>  
Switch>en  
Switch#clock set 11:55:00 june 19 2022  
Switch#conf t  
Switch(config)#hostname S1  
S1(config)#no ip domain-ookup  
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
Switch#clock set 11:55:00 june 19 2022  
Switch#conf t
Switch(config)#hostname S2
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


------------

### Часть 2. Настройка сетей VLAN на коммутаторах.

*Шаг 1. Создайте сети VLAN на коммутаторах.*

a.	Создайте необходимые VLAN и назовите их на каждом коммутаторе из приведенной выше таблицы

S1(config-vlan)#vlan 20   
S1(config-vlan)#name MGMT  
S1(config)#vlan 30  
S1(config-vlan)#name Operations  
S1(config)#vlan 999  
S1(config-vlan)#name ParkingLot  
S1(config-vlan)#vlan 1000  
S1(config-vlan)#name OWN  

S2(config)#vlan 20  
S2(config-vlan)#name MGMT  
S2(config-vlan)#vlan 40  
S2(config-vlan)#name Sales  
S2(config-vlan)#vlan 999  
S2(config-vlan)#name ParkingLot  
S2(config-vlan)#vlan 1000  
S2(config-vlan)#name Own  

b.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации.

S1(config)#ip default-gateway 10.20.0.1  
S1(config)#int vlan 20  
S1(config-if)#  
%LINK-5-CHANGED: Interface Vlan20, changed state to up 
S1(config-if)#ip address 10.20.0.2 255.255.255.0  

S2(config)#ip default-gateway 10.20.0.1  
S2(config)#int vlan 20  
S2(config-if)#  
%LINK-5-CHANGED: Interface Vlan20, changed state to up 
S2(config-if)#ip address 10.20.0.3 255.255.255.0  

c.	Назначьте все неиспользуемые порты коммутатора VLAN Parking Lot, настройте их для статического режима доступа и административно деактивируйте их.

S1(config)#int r fa0/2-4, fa0/7-24, g0/1-2  
S1(config-if-range)#switchport mode access   
S1(config-if-range)#switchport access vlan 999  
S1(config-if-range)#shu  

S2(config)#int r fa0/2-4, fa0/6-17, f0/19-24, g0/1-2  
S2(config-if-range)#switchport mode access   
S2(config-if-range)#switchport access vlan 999  
S2(config-if-range)#shu  

*Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора*

a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

S1(config)#int fa0/6  
S1(config-if)#switchport mode access   
S1(config-if)#switchport access vlan 30  
S1(config-if)#switchport nonegotiate   


S2(config)#int fa0/5  
S2(config-if)#switchport mode access   
S2(config-if)#switchport access vlan 20  
S2(config-if)#switchport nonegotiate   
S2(config-if)#int fa0/18  
S2(config-if)#switchport mode access   
S2(config-if)#switchport access vlan 40  
S2(config-if)#switchport nonegotiate   
b.	Выполните команду show vlan brief, чтобы убедиться, что сети VLAN назначены правильным интерфейсам.

**show vlan brief S1**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_11/Images/show%20vlan%20brief%20S1.jpg)

**show vlan brief S2**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_11/Images/show%20vlan%20brief%20S2.jpg)


------------


### Часть 3. ·Настройте транки (магистральные каналы).

*Шаг 1. Вручную настройте магистральный интерфейс F0/1.*

a.	Измените режим порта коммутатора на интерфейсе F0/1, чтобы принудительно создать магистральную связь. Не забудьте сделать это на обоих коммутаторах. и b. В рамках конфигурации транка установите для native vlan значение 1000 на обоих коммутаторах. При настройке двух интерфейсов для разных собственных VLAN сообщения об ошибках могут отображаться временно.

S1(config)#int fa0/1   
S1(config-if)#switchport mode trunk   
S1(config-if)#switchport trunk native vlan 1000  

S2(config)#int fa0/1  
S2(config-if)#switchport mode trunk   
S2(config-if)#switchport trunk native vlan 1000  

c.	В качестве другой части конфигурации транка укажите, что VLAN 10, 20, 30 и 1000 разрешены в транке.

S1(config-if)switchport trunk allowed vlan 20,30,40, 1000

S2(config-if)#switchport trunk allowed vlan 20,30,40,1000  

d.	Выполните команду show interfaces trunk для проверки портов магистрали, собственной VLAN и разрешенных VLAN через магистраль.

**show interfaces trunk S1**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_11/Images/show%20interface%20trunk%20S1.jpg)

**show interfaces trunk S2**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_11/Images/show%20interface%20trunk%20S2.jpg)

*Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.*

a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.

S1(config)#int fa0/5  
S1(config-if)#switchport mode trunk    
S1(config-if)#switchport trunk native vlan 1000    
S1(config-if)switchport trunk allowed vlan 20,30,,40,1000    

b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

S1(config-if)#do wr  
Building configuration...  
[OK]  

c.	Используйте команду show interfaces trunk для проверки настроек транка.

![Image]()


------------



### Часть 4. Настройте маршрутизацию.

*Шаг 1. Настройка маршрутизации между сетями VLAN на R1.*

a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.

R1(config)#int g0/0/1  
R1(config-if)#no shu  

b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейс для собственной VLAN не имеет назначенного IP-адреса. Включите описание для каждого подинтерфейса.

R1(config)#int g0/0/1.20  
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip address 10.20.0.1 255.255.255.0
R1(config-subif)#description MGMT  
R1(config)#int g0/0/1.30  
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip address 10.30.0.1 255.255.255.0 
R1(config-subif)#description Operations  
R1(config)#int g0/0/1.40  
R1(config-subif)#encapsulation dot1Q 40
R1(config-subif)#ip address 10.40.0.1 255.255.255.0
R1(config-subif)#description Sales  
R1(config)#int g0/0/1.1000  
R1(config-subif)#encapsulation dot1q  
R1(config-subif)#description Own  

c.	Настройте интерфейс Loopback 1 на R1 с адресацией из приведенной выше таблицы.

R1(config)#int Lo1  
R1(config-if)#ip address 172.16.1.1 255.255.255.0  

d.	С помощью команды show ip interface brief проверьте конфигурацию подынтерфейса.

![Image]()

*Шаг 2. Настройка интерфейса R2 g0/0/1 с использованием адреса из таблицы и маршрута по умолчанию с адресом следующего перехода 10.20.0.1*

R2(config)#ip route 0.0.0.0 0.0.0.0 10.20.0.1  
R2(config-if)#no shu  
R2(config-if)#ip address 10.20.0.4 255.255.255.0  


------------


### Часть 5. Настройте удаленный доступ

Шаг 1. Настройте все сетевые устройства для базовой поддержки SSH.

a.	Создайте локального пользователя с именем пользователя SSHadmin и зашифрованным паролем $cisco123!

R1(config)#username SSHadmin password $cisco123!   

R2(config)#username SSHadmin password $cisco123!   
 
S1(config)#username SSHadmin password $cisco123!   

S2(config)#username SSHadmin password $cisco123!   

b.	Используйте ccna-lab.com в качестве доменного имени. 
c.	Генерируйте криптоключи с помощью 1024 битного модуля.
соединения и с локальной аутентификацией.


R1(config)#ip domain-name ccna-lab.com  
R1(config)#crypto key generate  rsa general-keys modulus 1024  
R1(config)#line vty 0 4  
R1(config-line)#transport input ssh  

R2(config)#ip domain-name ccna-lab.com    
R2(config)#crypto key generate  rsa general-keys modulus 1024    
R2(config)#line vty 0 4    
R2(config-line)#transport input ssh    

S1(config)#ip domain-name ccna-lab.com    
S1(config)#crypto key generate  rsa general-keys modulus 1024   
S1(config)#line vty 0 4  
S1(config-line)#transport input ssh  


S2(config)#ip domain-name ccna-lab.com  
S2(config)#crypto key generate  rsa general-keys modulus 1024  
S2(config)#line vty 0 4  
S2(config-line)#transport input ssh  

*Шаг 2. Включите защищенные веб-службы с проверкой подлинности на R1.*

a.	Включите сервер HTTPS на R1.  
R1(config)# ip http secure-server   

b.	Настройте R1 для проверки подлинности пользователей, пытающихся подключиться к веб-серверу.

R1(config)# ip http authentication local  

**Команды не доступны в Packet Tracer.**


------------

### Часть 6. Проверка подключения

*Шаг 1. Настройте узлы ПК.*

![Image]()

![Image]()

*Шаг 2. Выполните следующие тесты. Эхозапрос должен пройти успешно.*

| От  | Протокол  | Назначение  | Результат|
| ------------ | ------------ | ------------ | ------------ |
| PC-A  | Ping  | 10.40.0.10  | Успешно  |
|  PC-A | Ping  | 10.20.0.1  |  Успешно |
|PC-B   | Ping  | 10.30.0.10  |Успешно   |
| PC-B  |  Ping | 10.20.0.1  | Успешно  |
|PC-B| Ping  | 172.16.1.1  |Успешно   |
|  PC-B |   HTTPS   | 10.20.0.1  | Неуспешно  |
| PC-B  |  HTTPS | 172.16.1.1  | Неуспешно   |
| PC-B  |SSH | 10.20.0.1  | Успешно  |
|PC-B   |  SSH |172.16.1.1   | Успешно  |


------------



### Часть 7. Настройка и проверка списков контроля доступа (ACL)
*Шаг 1. Проанализируйте требования к сети и политике безопасности для планирования реализации ACL.  
 Шаг 2. Разработка и применение расширенных списков доступа, которые будут соответствовать требованиям политики безопасности.
Откройте окно конфигурации*
 


S2(config)#ip access-list extended Sales
S2(config-ext-nacl)#deny tcp 10.40.0.0 0.255.255.255 10.20.0.0 0.255.255.255 eq 22 
S2(config-ext-nacl)#deny tcp 10.40.0.0 0.255.255.255 10.20.0.0 0.255.255.255 eq www 
S2(config-ext-nacl)#deny tcp 10.40.0.0 0.255.255.255 10.20.0.0 0.255.255.255 eq 443
S2(config-ext-nacl)#deny icmp 10.40.0.0 0.255.255.255 10.20.0.0 0.255.255.255
S2(config-ext-nacl)#deny icmp 10.40.0.0 0.255.255.255 10.40.0.0 0.255.255.255
S2(config-ext-nacl)#permit icmp 10.40.0.0 0.255.255.255 any
S2(config-ext-nacl)#permit ip any any 

S1(config)ip access-list extended Operations
S1(config-ext-nacl)#deny icmp 10.30.0.0 0.255.255.255 10.40.0.0 0.255.255.255
S1(config-ext-nacl)#permit icmp 10.30.0.0 0.255.255.255 any


*Шаг 3. Убедитесь, что политики безопасности применяются развернутыми списками доступа.*

| От  | Протокол  | Назначение  | Результат|
| ------------ | ------------ | ------------ | ------------ |
| PC-A  | Ping  | 10.40.0.10  | Сбой  |
|  PC-A | Ping  | 10.20.0.1  |  Успешно |
|PC-B   | Ping  | 10.30.0.10  |Сбой   |
| PC-B  |  Ping | 10.20.0.1  |Сбой  |
|PC-B| Ping  | 172.16.1.1  |Успешно   |
|  PC-B |   HTTPS   | 10.20.0.1  | Не реализовано в CPT  |
| PC-B  |  HTTPS | 172.16.1.1  | Не реализовано в CPT    |
| PC-B  |SSH | 10.20.0.1  | Сбой  |
|PC-B   |  SSH |172.16.1.1   | Успешно  |
