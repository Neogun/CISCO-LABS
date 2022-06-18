# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями. 
**Выполнил Давыдкин Д.Г.**

### Топология

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/Topology.jpg)

### Таблица адресации

| Устройство  | Интерфейс  | IP-адрес  |  Маска подсети | Шлюз по умолчанию  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| R1  | G0/0/1.10  | 192.168.10.1  | 255.255.255.0  | - |
| R1  | G0/0/1.20  | 192.168.20.1  |255.255.255.0   | -  |
| R1  | G0/0/1.30  |192.168.30.1    | 255.255.255.0  |  - |
|R1   | G0/0/1.1000 |  - |  - | -  |
|S1   | VLAN 10  | 192.168.10.11  | 255.255.255.0  | 192.168.10.1  |
| S2  | VLAN 10   | 192.168.10.12  |255.255.255.0  |192.168.10.1 |
| PC-A  | NIC  |  192.168.20.3 |255.255.255.0   | 192.168.20.1  |
| PC-B  | NIC  |  192.168.30.3 | 255.255.255.0  | 192.168.30.1  |

### Таблица VLAN

| VLAN  | Имя  | Назначенный интерфейс  |
| ------------ | ------------ | ------------ |
| 10  |Управление | S1: VLAN 10 S2: VLAN 10   |
| 20  |  Sales | S1: F0/6  |
| 30 |  Operations | S2: F0/18  |
|999   |  Parking_Lot | С1: F0/2-4, F0/7-24, G0/1-2 С2: F0/2-17, F0/19-24, G0/1-2  |
|1000 | Собственная | -  |

### Задачи

Часть 1. Создание сети и настройка основных параметров устройства
Часть 2. Создание сетей VLAN и назначение портов коммутатора
Часть 3. Настройка транка 802.1Q между коммутаторами.
Часть 4. Настройка маршрутизации между сетями VLAN
Часть 5. Проверка, что маршрутизация между VLAN работает

### Часть 1. Создание сети и настройка основных параметров устройства

*Шаг 1. Создайте сеть согласно топологии.*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/Network%20PT.jpg)

*Шаг 2. Настройте базовые параметры для маршрутизатора.*

Would you like to enter the initial configuration dialog? [yes/no]: n  
Press RETURN to get started!  
Router>en  
Router#clock set 18:00:00 june 18 2022  
Router#conf t  
Router(config)#hostname R1  
R1(config)#no ip domain lookup   
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
R1(config)#int g0/0/1  
R1(config-if)#no shu  
R1(config-if)#do wr  
R1(config-if)#no shu  

*Шаг 3. Настройте базовые параметры каждого коммутатора.*

**Настройка S1  **

Switch>  
Switch>en  
Switch#clock set 18:10:00 june 18 2022  
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
 
**Настройка S2  **

Switch>  
Switch>en  
Switch#clock set 18:10:00 june 18 2022  
Switch#conf t  
Switch(config)#hostname S1  
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
S2(config-line)#do wr  

*Шаг 4. Настройте узлы ПК.*

**Настройка PC-A  **

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/PC-A%20Config.jpg)

**Настройка PC-B  **

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/PC-B%20Config.jpg)

### Часть 2. Создание сетей VLAN и назначение портов коммутатора и

*Шаг 1. Создайте сети VLAN на коммутаторах. и Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.* 

**Настройка S1  **

User Access Verification  
Password:  
S1>en  
Password:    
S1#conf t  
S1(config)#ip default-gateway 192.168.10.1  
S1(config)#vlan 10  
S1(config-vlan)#name Management   
S1(config-vlan)#int vlan 10  
S1(config-if)#ip address 192.168.10.11 255.255.255.0  
S1(config-if)#description MGMT  
S1(config)#vlan 20  
S1(config-vlan)#name Sales  
S1(config-if-range)#vlan 30  
S1(config-vlan)#name Operations  
S1(config-vlan)#vlan 999  
S1(config-vlan)#name Parking_Lot  
S1(config-vlan)#vlan 1000  
S1(config-vlan)#name Own  
S1(config-vlan)#int f0/6  
S1(config-if)#switchport mode access   
S1(config-if)#switchport access vlan 20  
S1(config-if)#int range F0/2-4, F0/7-24, G0/1-2  
S1(config-if-range)#switchport mode access  
S1(config-if-range)#switchport access vlan 999  
S1(config-if-range)#shu  
S1(config-if)#do wr  
Building configuration...  
[OK]  

**Настройка S2  **

User Access Verification  
Password:  
S2>en  
Password:    
S2#conf t  
S2(config)#ip default-gateway 192.168.10.1  
S2(config)#vlan 10  
S2(config-vlan)#name Management   
S2(config-vlan)#int vlan 10  
S2(config-if)#ip address 192.168.10.12 255.255.255.0  
S2(config-if)#description MGMT  
S2(config)#vlan 20  
S2(config-vlan)#name Sales  
S2(config-if-range)#vlan 30  
S2(config-vlan)#name Operations  
S2(config-vlan)#vlan 999  
S2(config-vlan)#name Parking_Lot  
S2(config-vlan)#vlan 1000  
S2(config-vlan)#name Own  
S2(config-vlan)#int f0/18  
S2(config-if)#switchport mode access   
S2(config-if)#switchport access vlan 30  
S2(config-if)#F0/2-17, F0/19-24, G0/1-2  
S2(config-if-range)#switchport mode access  
S2(config-if-range)#switchport access vlan 999  
S2(config-if-range)#shu  
S2(config-if)#do wr  
Building configuration...  
[OK]  


b.	Убедитесь, что VLAN назначены на правильные интерфейсы.

**show VLAN S1**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/show%20VLAN%20S1.jpg)

**show VLAN S2**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/show%20VLAN%20S2.jpg)


### Часть 3. Конфигурация магистрального канала стандарта 802.1Q между коммутаторами

*Шаг 1. Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2.*

S1(config)#int fa0/1  
S1(config-if)#switchport mode trunk   
S1(config-if)#switchport trunk native vlan 1000  
S1(config-if)#switchport trunk allowed vlan 10,20,30,1000  
S1(config-if)#do wr  
Building configuration...  
[OK]  


S2(config)#int fa0/1  
S2(config-if)#switchport mode trunk   
S2(config-if)#switchport trunk native vlan 1000  
S2(config-if)#switchport trunk allowed vlan 10,20,30,1000  
S2(config-if)#do wr  
Building configuration...  
[OK]  

*Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.*  

*a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора. и b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.*

S1(config)#int fa0/5  
S1(config-if)#switchport mode trunk   
S1(config-if)#switchport trunk native vlan 1000  
S1(config-if)#switchport trunk allowed vlan 10,20,30,1000  
S1(config-if)#do wr  
Building configuration...  
[OK]  

*c.	Проверка транкинга.*
Что произойдет, если G0/0/1 на R1 будет отключен? **Интерфейс на коммутаторе тоже будет отключен.**

### Часть 4. Настройка маршрутизации между сетями VLAN

R1(config)#int g0/0/1  
R1(config-if)#no shu  
R1(config-if)#int g0/0/1.10  
R1(config-subif)#description VLAN 10 MGMT  
R1(config-subif)#encapsulation dot1Q 10  
R1(config-subif)#ip address 192.168.10.1 255.255.255.0  
R1(config-if)#int g0/0/1.20  
R1(config-subif)#description VLAN 20 Sales  
R1(config-subif)#encapsulation dot1Q 20  
R1(config-subif)#ip address 192.168.20.1 255.255.255.0  
R1(config-if)#int g0/0/1.30   
R1(config-subif)#description VLAN 30 Operations  
R1(config-subif)#encapsulation dot1Q 30  
R1(config-subif)#ip address 192.168.30.1 255.255.255.0  
R1(config-if)#int g0/0/1.1000  
R1(config-subif)#description VLAN TRUNK  
R1(config-subif)#encapsulation dot1Q 1000  
R1(config-subif)#do wr
Building configuration...  
[OK]  

*c.	Убедитесь, что вспомогательные интерфейсы работают*


![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/show%20ip%20interface%20R1.jpg)

### Часть 5. Проверьте, работает ли маршрутизация между VLAN

*Шаг 1. Выполните следующие тесты с PC-A. Все должно быть успешно.*

a.	Отправьте эхо-запрос с PC-A на шлюз по умолчанию.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/PING%20PC-A%20to%20default-gateway.jpg)

b.	Отправьте эхо-запрос с PC-A на PC-B.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/PING%20PC-A%20to%20PC-B.jpg)

c.	Отправьте команду ping с компьютера PC-A на коммутатор S2.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/PING%20PC-A%20to%20S2.jpg)

*Шаг 2. Пройдите следующий тест с PC-B*
В окне командной строки на PC-B выполните команду tracert на адрес PC-A.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_06/Images/tracert%20PC-B%20to%20PC-A.jpg)

Какие промежуточные IP-адреса отображаются в результатах?
**Адрес сабинтерфейса для VLAN 30 на R1. Дальше пакет идёт на L2 S1 уже на PC-A.**
