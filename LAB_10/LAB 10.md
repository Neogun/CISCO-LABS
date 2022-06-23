# Лабораторная работа. Настройка протокола OSPFv2 для одной области

**Выполнил Давыдкин Д.Г.**

### Топология

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_10/Images/Topology.jpg)


------------

### Таблица адресации

| Устройство  | Интерфейс  | IP-адрес  | Маска подсети   |
| ------------ | ------------ | ------------ | ------------ |
| R1  | G0/0/1  | 10.53.0.1  | 255.255.255.0  |
| R1  | Loopback1  | 172.16.1.1  | 255.255.255.0  |
| R2  | G0/0/1  |10.53.0.2  |255.255.255.0   |
| R2  |Loopback1   | 192.168.1.1  | 255.255.255.0  |


------------

### Цели


Часть 1. Создание сети и настройка основных параметров устройства
Часть 2. Настройка и проверка базовой работы протокола  OSPFv2 для одной области
Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области


------------
### Часть 1. Создание сети и настройка основных параметров устройства

*Шаг 1. Создайте сеть согласно топологии.*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_10/Images/Network%20.jpg)

*Шаг 2. Произведите базовую настройку маршрутизаторов.*

**Настройка R1**

Would you like to enter the initial configuration dialog? [yes/no]: n  
Press RETURN to get started!  
Router>en  
Router#clock set 19:00:00 june 20 2022  
Router#conf t  
Router(config)#hostname R1  
R1(config)#no ip domain-lookup  
R1(config)#enable secret class  
R1(config)#service password-encryption  
R1(config)#banner motd #ENTRY CLOSED!!!#  
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
R1(config-if)#do wr  
R1(config-if)#no shu  


**Настройка R2**

Would you like to enter the initial configuration dialog? [yes/no]: n  
Press RETURN to get started!  
Router>en  
Router#clock set 19:05:00 june 20 2022  
Router#conf t  
Router(config)#hostname R1 
R2(config)#no ip domain-lookup  
R2(config)#enable secret class  
R2(config)#service password-encryption  
R2(config)#banner motd #ENTRY CLOSED!!!#  
R2(config)#line con 0  
R2(config-line)#logging synchronous  
R2(config-line)#password cisco  
R2(config-line)#login  
R2(config)#line vty 0 15  
R2(config-line)#logging synchronous  
R2(config-line)#password cisco  
R2(config-line)#login  
R2(config)#int g0/0/1 
R2(config-if)#no shu  
R2(config-if)#do wr  
R2(config-if)#no shu  
R2(config-if)#do wr  
R2(config-if)#no shu  

*Шаг 3. Настройте базовые параметры каждого коммутатора.*

**Настройка S1**
Switch>
Switch>en
Switch#clock set 19:15:00 june 20 2022
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
S1(config-line)#do wr
Building configuration...
[OK]

**Настройка S2**

Switch>
Switch>en
Switch#clock set 19:20:00 june 20 2022
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
S2(config-line)#do wr  
Building configuration...
[OK]

------------



### Часть 2. Настройка и проверка базовой работы протокола OSPFv2 для одной области

*Шаг 1. Настройте адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе.*

a.	Настройте адреса интерфейсов на каждом маршрутизаторе, как показано в таблице адресации.

**Настройка R1**

R1(config)#int g0/0/1  
R1(config-if)#ip address 10.53.0.1 255.255.255.0  
R1(config-if)#int Loopback1  
R1(config-if)#ip address 172.16.1.1 255.255.255.0   
R1(config-if)#no shu  

**Настройка R2**

R2(config)#int g0/0/1  
R2(config-if)#ip address 10.53.0.1 255.255.255.0  
R2(config-if)#int Loopback1  
R2(config-if)#ip address 192.168.1.1 255.255.255.0   
R2(config-if)#no shu  

b.	Перейдите в режим конфигурации маршрутизатора OSPF, используя идентификатор процесса 56. и c.	Настройте статический идентификатор маршрутизатора для каждого маршрутизатора (1.1.1.1 для R1, 2.2.2.2 для R2).

R1(config)#router ospf 56 
R1(config-router)#router-id 1.1.1.1  

R2(config)#router ospf 56  
R2(config-router)#router-id 2.2.2.2  

d.	Настройте инструкцию сети для сети между R1 и R2, поместив ее в область 0.

R1(config)#router ospf 56  
R1(config-router)#network 10.53.0.0 0.0.0.255 area 0

R2(config)#router ospf 56  
R2(config-router)#network 10.53.0.0 0.0.0.255 area 0

e.	Только на R2 добавьте конфигурацию, необходимую для объявления сети Loopback 1 в область OSPF 0.

R2(config)#router ospf 56  
R2(config-router)#network 192.168.1.0 0.0.0.255 area 0  

f.	Убедитесь, что OSPFv2 работает между маршрутизаторами. Выполните команду, чтобы убедиться, что R1 и R2 сформировали смежность.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_10/Images/show%20ip%20ospf%20neighbor.jpg)

Какой маршрутизатор является DR? Какой маршрутизатор является BDR? Каковы критерии отбора?

g.	На R1 выполните команду show ip route ospf, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание, что поведение OSPF по умолчанию заключается в объявлении интерфейса обратной связи в качестве маршрута узла с использованием 32-битной маски.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_10/Images/show%20ip%20route%20ospf.jpg)

h.	Запустите Ping до  адреса интерфейса R2 Loopback 1 из R1. Выполнение команды ping должно быть успешным.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_10/Images/ping%20R1%20to%20Loopback%201%20R2.jpg)



------------

### Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области

*Шаг 1. Реализация различных оптимизаций на каждом маршрутизаторе.*

a.	На R1 настройте приоритет OSPF интерфейса G0/0/1 на 50, чтобы убедиться, что R1 является назначенным маршрутизатором.

R1(config)#int g0/0/1  
R1(config-if)#ip ospf priority 50  

b.	Настройте таймеры OSPF на G0/0/1 каждого маршрутизатора для таймера приветствия, составляющего 30 секунд.

R1(config)#int g0/0/1  
R1(config-if)#ip ospf hello-interval 30  


R2(config)#int g0/0/1  
R2(config-if)#ip ospf hello-interval 30  

c.	На R1 настройте статический маршрут по умолчанию, который использует интерфейс Loopback 1 в качестве интерфейса выхода. Затем распространите маршрут по умолчанию в OSPF. Обратите внимание на сообщение консоли после установки маршрута по умолчанию.

R1(config)#ip route 0.0.0.0 0.0.0.0 loopback1

d.	добавьте конфигурацию, необходимую для OSPF для обработки R2 Loopback 1 как сети точка-точка. Это приводит к тому, что OSPF объявляет Loopback 1 использует маску подсети интерфейса.

R2(config)#int loopback 1  
R2(config-if)#ip ospf network point-to-point  

e.	Только на R2 добавьте конфигурацию, необходимую для предотвращения отправки объявлений OSPF в сеть Loopback 1.

R2(config)#router ospf 56
R2(config-router)#passive-interface loopback1

f.	Измените базовую пропускную способность для маршрутизаторов. После этой настройки перезапустите OSPF с помощью команды clear ip ospf process . Обратите внимание на сообщение консоли после установки новой опорной полосы пропускания.

R1(config-router)# auto-cost reference-bandwidth 10000   
% OSPF: Reference bandwidth is changed.  
        Please ensure reference bandwidth is consistent across all routers.  

R2(config-router)#auto-cost reference-bandwidth 10000  
% OSPF: Reference bandwidth is changed.  
        Please ensure reference bandwidth is consistent across all routers.  
R2(config-router)#  

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_10/Images/clear%20ospf%20R1.jpg)


![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_10/Images/clear%20ospf%20R2.jpg)
