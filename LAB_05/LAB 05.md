# Лабораторная работа. Доступ к сетевым устройствам по протоколу SSH

**Выполнил Давыдкин Д.Г.**

### Топология

![Image]()

------------

### Таблица адресации

| Устройство  | Интерфейс  | IP-адрес  | Маска подсети  | Шлюз по умолчанию |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| R1  | G/0/0/1  | 192.168.1.1  | 255.255.255.0  | -  |
| S1  | VLAN 1 | 192.168.1.11 | 255.255.255.0    |  192.168.1.1  |
| PC-A  |  NIC | 192.168.1.3  | 255.255.255.0    | 192.168.1.1   |


------------

### Задачи

Часть 1. Настройка основных параметров устройства
Часть 2. Настройка маршрутизатора для доступа по протоколу SSH
Часть 3. Настройка коммутатора для доступа по протоколу SSH
Часть 4. SSH через интерфейс командной строки (CLI) коммутатора


------------

### Часть 1. Настройка основных параметров устройств

*Шаг 1. Создайте сеть согласно топологии.*

![Image]()

*Шаг 2. Выполните инициализацию и перезагрузку маршрутизатора и коммутатора.*

Would you like to enter the initial configuration dialog? [yes/no]: n
Router>
Router>en
Router#erase startup-config 
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]y[OK]
Erase of nvram: complete
Router#reload
System configuration has been modified. Save? [yes/no]:n
Proceed with reload? [confirm]y


Switch>
Switch>en
Switch#erase startup-config
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]y[OK]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
Switch#reload
System configuration has been modified. Save? [yes/no]:n
Proceed with reload? [confirm]y

*Шаг 3. Настройте маршрутизатор.*

Router>
Router>en
Router#clock set 19:05:00 june 15 2022
Router#conf t
Router(config)#hostname R1
R1(config)#no ip domain-lookup 
R1(config)#banner motd #Entry closed!!!#
R1(config)#enable secret class
R1(config)#service password-encryption 
R1(config)#line con 0
R1(config-line)#logging synchronous
R1(config-line)#password cisco
R1(config-line)#login
R1(config)#line vty 0 15
R1(config-line)#logging synchronous
R1(config-line)#password cisco
R1(config-line)#login
R1(config)#int g0/0/1
R1(config-if)#no shu
R1(config-if)#ip address 192.168.1.1 255.255.255.0
R1(config-if)#do wr


*Шаг 4. Настройте компьютер PC-A.*

![Image]()

*Шаг 5. Проверьте подключение к сети.*

![Image]()

### Часть 2. Настройка маршрутизатора для доступа по протоколу SSH

*Шаг 1. Настройте аутентификацию устройств.*

Entry closed!!!
User Access Verification
Password: 
R1>en
Password: 
R1#conf t
R1(config)#hostname R1
R1(config)#ip domain name LAB05.local

*Шаг 2. Создайте ключ шифрования с указанием его длины.*

R1(config)#crypto key generate rsa

How many bits in the modulus [512]: 1024

% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

*Шаг 3. Создайте имя пользователя в локальной базе учетных записей.*

R1(config)#username admin privilege 15 password  Adm1nP @55


*Шаг 4. Активируйте протокол SSH на линиях VTY.*

S1(config)#ip ssh version 2
R1(config)#line vty 0 15
R1(config-line)#transport input telnet ssh    ** !~ В Packet Tracer маршрутизатор не принимал команду. Пришлось задейстовать с параметром ALL. По заданию надо задейстовать telnet и ssh.**
R1(config-line)#transport input all
R1(config-line)#login local

Шаг 5. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

*R1(config-line)#do wr*
Building configuration...
[OK]

*Шаг 6. Установите соединение с маршрутизатором по протоколу SSH.*

![Image]()
![Image]()


------------


### Часть 3. Настройка коммутатора для доступа по протоколу SSH

*Шаг 1. Настройте основные параметры коммутатора.*

Switch>
Switch>en
Router#clock set 19:37:00 june 15 2022
Switch#conf t
Switch(config)#hostname S1
S1(config)#no ip domain-lookup 
S1(config)#banner motd #Entry closed!!!#
S1(config)#enable secret class
S1(config)#  line con 0
S1(config-line)#logging synchronous 
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#line vty 0 15
S1(config-line)#logging synchronous 
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption 
S1(config)#int vlan 1
S1(config-if)#no shu
S1(config-if)#ip address 192.168.11 255.255.255.0

*Шаг 2. Настройте коммутатор для соединения по протоколу SSH.*

S1(config)#hostname S1
S1(config)#ip domain name LAB05.local
S1(config)#crypto key generate rsa
How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
S1(config)#username admin privilege 15 password Adm1nP @55
S1(config)#ip ssh version 2
S1(config)#line vty 0 15
S1(config-line)#transport input all
S1(config-line)#login local
S1(config-line)#do wr
Building configuration...
[OK]

*Шаг 3. Установите соединение с коммутатором по протоколу SSH.*

![Image]()
![Image]()

Удалось ли вам установить SSH-соединение с коммутатором? **Да, удалось.**


------------

### Часть 4. Настройка протокола SSH с использованием интерфейса командной строки (CLI) коммутатора
*
Шаг 1. Посмотрите доступные параметры для клиента SSH в Cisco IOS.*

S1# ssh? 
  -c Select encryption algorithm
  -l Log in using this user name
  -m Select HMAC algorithm
  -o Specify options
  -p Connect to this port
  -v Specify SSH Protocol Version
  -vrf Specify vrf name
  WORD IP-адрес или имя хоста удаленной системы

В моём случае было доступно меньшее количество аргументов. Возможно ограничение Cisco Packet Tracer.

S1#ssh ?
  -l  Log in using this user name
  -v  Specify SSH Protocol Version

*Шаг 2. Установите с коммутатора S1 соединение с маршрутизатором R1 по протоколу SSH.*
a) 
![Image]()

b,c,d) 
![Image]()

![Image]()

Какие версии протокола SSH поддерживаются при использовании интерфейса командной строки? **Первая и вторая версия протокола SSH.**


------------
### Вопрос для повторения

Как предоставить доступ к сетевому устройству нескольким пользователям, у каждого из которых есть собственное имя пользователя? **Создать сразу несколько локальных пользователей со своим уровнем привелегий. Но самый правильным вариантом, будет использование сервера ААА.**
