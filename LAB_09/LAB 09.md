# Лабораторная работа - Конфигурация безопасности коммутатора 

**Выполнил Давыдкин Д.Г.**


------------

### Топология

![image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/Topology.jpg)


------------
### Таблица адресации

| Устройство  | interface/vlan  | IP-адрес |  Маска подсети |
| ------------ | ------------ | ------------ | ------------ |
| R1  |G0/0/1   |192.168.10.1   |255.255.255.0   |
|  R1 | Loopback 0   |10.10.1.1   |255.255.255.0   |
| S1  |  VLAN 10 | 192.168.10.201  | 255.255.255.0  |
|  S2 | VLAN 10  | 192.168.10.202  | 255.255.255.0  |
|  PC-A |  NIC | DHCP  | 255.255.255.0  |
|  PC-B | NIC  | DHCP  | 255.255.255.0  |


------------
### Цели


Часть 1. Настройка основного сетевого устройства
•	Создайте сеть.
•	Настройте маршрутизатор R1.
•	Настройка и проверка основных параметров коммутатора
Часть 2. Настройка сетей VLAN
•	Сконфигруриуйте VLAN 10.
•	Сконфигруриуйте SVI для VLAN 10.
•	Настройте VLAN 333 с именем Native на S1 и S2.
•	Настройте VLAN 999 с именем ParkingLot на S1 и S2.
Часть 3: Настройки безопасности коммутатора.
•	Реализация магистральных соединений 802.1Q.
•	Настройка портов доступа
•	Безопасность неиспользуемых портов коммутатора
•	Документирование и реализация функций безопасности порта.
•	Реализовать безопасность DHCP snooping .
•	Реализация PortFast и BPDU Guard
•	Проверка сквозной связанности.


------------


### Часть 1. Настройка основного сетевого устройства

*Шаг 1. Создайте сеть.*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/Network%20PT.jpg)

b.	Инициализация устройств

Router>  
Router>en 
Router#erase startup-config   
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]y[OK]  
Erase of nvram: complete  
Router#reload  
Proceed with reload? [confirm]yInitializing Hardware ...  

Switch>  
Switch>en  
Switch#erase startup-config   
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]y[OK]  
Erase of nvram: complete  
Switch#reload  
Proceed with reload? [confirm]yC2960 Boot Loader (C2960-HBOOT-M) Version   12.2(25r)FX, RELEASE SOFTWARE (fc4)  

*Шаг 2. Настройте маршрутизатор R1

Router>  
Router>enable  
Router#configure terminal  
Enter configuration commands, one per line.  End with CNTL/Z.  
Router(config)#hostname R1  
R1(config)#no ip domain lookup  
R1(config)#ip dhcp excluded-address 192.168.10.1 192.168.10.9  
R1(config)#ip dhcp excluded-address 192.168.10.201 192.168.10.202  
R1(config)#!  
R1(config)#ip dhcp relay information trust-all  
R1(config)#ip dhcp pool Students  
R1(dhcp-config)# network 192.168.10.0 255.255.255.0  
R1(dhcp-config)# default-router 192.168.10.1  
R1(dhcp-config)# domain-name CCNA2.Lab-11.6.1  
R1(dhcp-config)#!  
R1(dhcp-config)#interface Loopback0  

R1(config-if)# ip address 10.10.1.1 255.255.255.0  
R1(config-if)#!  
R1(config-if)#interface GigabitEthernet0/0/1  
R1(config-if)# description Link to S1  
R1(config-if)# ip address 192.168.10.1 255.255.255.0  
R1(config-if)# no shutdown  
R1(config-if)#!  
R1(config-if)#line con 0  
R1(config-line)# logging synchronous  
R1(config-line)# exec-timeout 0 0  
R1(config-line)#  
%LINK-5-CHANGED: Interface Loopback0, changed state to up  
R1(config)#do wr  
Building configuration...  
[OK]  

*b.	Проверьте текущую конфигурацию на R1*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/show%20IP%20Interface%20brief%20R1.jpg)

*c.	Убедитесь, что IP-адресация и интерфейсы находятся в состоянии up / up (при необходимости устраните неполадки).*

**Интерфейсы в состоянии up/up.**

*Шаг 3. Настройка и проверка основных параметров коммутатора*

**Настройка S1**

Switch>  
Switch>en  
Switch#conf t  
Switch(config)#hostname S1  
S1(config)#no ip domain-lookup   
S1(config)#int fa0/1  
S1(config-if)#description Link to S2  
S1(config-if)#int fa0/5  
S1(config-if)#description Link to R1  
S1(config-if)#int fa0/6  
S1(config-if)#description Link to PC-A  
S1(config)#ip default-gateway 192.168.10.1  
S1(config)#do wr  

**Настройка S2**

Switch>  
Switch>en  
Switch#conf t  
Switch(config)#hostname S2  
S2(config)#no ip domain-lookup   
S2(config)#int fa0/1  
S2(config-if)#description Link to S1  
S2(config-if)#int fa0/18  
S2(config-if)#description Link to PC-B  
S1(config)#ip default-gateway 192.168.10.1  
S1(config)#do wr  

------------

### Часть 2. Настройка сетей VLAN на коммутаторах.

*Шаг 1. Сконфигруриуйте VLAN 10.*

S1(config)#vlan 10  
S1(config-vlan)#name Management  

S2(config)#vlan 10  
S2(config-vlan)#name Management  

*Шаг 2. Сконфигруриуйте SVI для VLAN 10.*

S1(config)#int vlan 10  
S1(config-if)#%LINK-5-CHANGED: Interface Vlan10, changed state to up  
S1(config-if)#ip address 192.168.10.201 255.255.255.0  
S1(config-if)#no shu  

S2(config)#int vlan 10  
S2(config-if)#%LINK-5-CHANGED: Interface Vlan10, changed state to up  
S2(config-if)#ip address 192.168.10.202 255.255.255.0  
S2(config-if)#no shu  

*Шаг 3. Настройте VLAN 333 с именем Native на S1 и S2.*

S1(config)#vlan 333  
S1(config-vlan)#name Native  

S2(config)#vlan 333  
S2(config-vlan)#name Native  

*Шаг 4. Настройте VLAN 999 с именем ParkingLot на S1 и S2.*

S1(config)#vlan 999  
S1(config-vlan)#name ParkingLot   


S2(config)#vlan 999  
S2(config-vlan)#name ParkingLot  


------------

### Часть 3. Настройки безопасности коммутатора.

*Шаг 1. Релизация магистральных соединений 802.1Q.*

S1(config)#int fa0/1  
S1(config-if)#switchport mode trunk   
S1(config-if)#switchport trunk native vlan 333  

S2(config)#int fa0/1  
S2(config-if)#switchport mode trunk   
S2(config-if)#switchport trunk native vlan 333  

**show interface trunk S1  **
![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/show%20interface%20Trunk%20S1.jpg)

**show interface trunk S2  **
![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/show%20interface%20Trunk%20S2.jpg)

*c.	Отключить согласование DTP F0/1 на S1 и S2. *

S1(config)#int fa0/1
S1(config-if)#switchport nonegotiate 

S2(config)#int fa0/1
S2(config-if)#switchport nonegotiate 

*Шаг 2. Настройка портов доступа*

a.	На S1 настройте F0/5 и F0/6 в качестве портов доступа и свяжите их с VLAN 10.

S1(config)#int r fa0/5-6  
S1(config-if-range)#switchport mode access   
S1(config-if-range)#switchport access vlan 10

b.На S2 настройте порт доступа Fa0/18 и свяжите его с VLAN 10

S2(config)#int fa0/18  
S2(config-if)#switchport mode access   
S2(config-if)#switchport access vlan 10  


*Шаг 3. Безопасность неиспользуемых портов коммутатора
*

a.	На S1 и S2 переместите неиспользуемые порты из VLAN 1 в VLAN 999 и отключите неиспользуемые порты.

S1(config)#int r fa 0/2-4, fa0/7-24, gig0/1-2  
S1(config-if-range)#switchport mode access   
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shu

S2(config)#int r fa0/2-17, fa0/19-24, gig0/1-2  
S2(config-if-range)#switchport mode access   
S2(config-if-range)#switchport access vlan 999  
S2(config-if-range)#shu  

b.	Убедитесь, что неиспользуемые порты отключены и связаны с VLAN 999, введя команду  show.

**show interfaces status S1**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/show%20interface%20status%20S1.jpg)

**show interfaces status S2**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/show%20interface%20status%20S2.jpg)

*Шаг 4. Документирование и реализация функций безопасности порта.*

a.	На S1, введите команду show port-security interface f0/6  для отображения настроек по умолчанию безопасности порта для интерфейса F0/6. Запишите свои ответы ниже.

| Конфигурация безопасности   | порта по умолчанию  |
| ------------ | ------------ |
| **Функция**  | **Настройка по умолчанию**  |
| Защита портов  | Disabled  |
| Максимальное количество записей MAC-адресов  | 1  |
| Режим проверки на нарушение безопасности  |Shutdown   |
|Aging Time   | 0 mins  |
| Aging Type  | Disabled  |
| Secure Static Address Aging  |Disabled   |
| Sticky MAC Address  | 0  |

b.	На S1 включите защиту порта на F0 / 6 со следующими настройками:

S1(config)#int fa0/6  
S1(config-if)#switchport port-security  
S1(config-if)#switchport port-security maximum 3  
S1(config-if)#switchport port-security aging time 60  
S1(config-if)#switch port-security violation restrict   

**!~Aging type отсутствует в PacketTracer**

c.	Verify port security on S1 F0/6.

![image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/show%20port-security%20fa0.6%20S1.jpg)

d.	Включите безопасность порта для F0 / 18 на S2. Настройте каждый активный порт доступа таким образом, чтобы он автоматически добавлял адреса МАС, изученные на этом порту, в текущую конфигурацию. и e.	Настройте следующие параметры безопасности порта на S2 F / 18:

S1(config)#int fa0/6  
S1(config-if)#switchport port-security mac-address sticky  

S2(config)#int fa0/18  
S2(config-if)#switchport port-security   
S2(config-if)#switchport port-security mac-address sticky  
S2(config-if)#switchport port-security aging time 60  
S2(config-if)#switchport port-security violation protect  
S2(config-if)#switchport port-security maximum 2  

f.	Проверка функции безопасности портов на S2 F0/18.

**show port-security interface f0/18**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/show%20port-security%20interface%20f0.18%20S2.jpg)

**show port-security address S2**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/show%20port-security%20address%20S2.jpg)

*Шаг 5. Реализовать безопасность DHCP snooping*
a.	На S2 включите DHCP snooping и настройте DHCP snooping во VLAN 10.

S2(config)#ip dhcp snooping   
S2(config)#ip dhcp snooping vlan 10  

S2(config-if)#ip dhcp snooping trust  
S2(config)#int fa0/1  

c.	Ограничьте ненадежный порт Fa0/18 на S2 пятью DHCP-пакетами в секунду.

S2(config-if)#ip dhcp snooping limit rate 5

d.	Проверка DHCP Snooping на S2.

**show ip dhcp snooping S2**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/show%20ip%20dhcp%20snooping%20S2.jpg)

e.	В командной строке на PC-B освободите, а затем обновите IP-адрес.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/PC-B%20release%20and%20renew.jpg)

f.	Проверьте привязку отслеживания DHCP с помощью команды show ip dhcp snooping binding.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/show%20ip%20dhcp%20snooping%20binding%20S2.jpg)

*Шаг 6. Реализация PortFast и BPDU Guard*

a.	Настройте PortFast на всех портах доступа, которые используются на обоих коммутаторах.

S1(config-if)#int r fa0/5-6  
S1(config-if-range)#spanning-tree portfast  

S2(config)#int fa0/18  
S2(config-if)#spanning-tree portfast  


b.	Включите защиту BPDU на портах доступа VLAN 10 S1 и S2, подключенных к PC-A и PC-B.

S1(config)#int fa0/6 
S1(config-if)#spanning-tree bpduguard enable  

S2(config)#int fa0/18  
S2(config-if)#spanning-tree bpduguard enable  

c.	Убедитесь, что защита BPDU и PortFast включены на соответствующих портах.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/Portfast%20fa0.6%20S1.jpg)

Шаг 7. Проверьте наличие сквозного ⁪подключения.

Проверка проблем не выявила. Проверку осуществил путём команды пинг на все IP-адреса с PC-A.

![image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_09/Images/PING%20PC-A.jpg)


------------


### Вопросы для повторения
1.	С точки зрения безопасности порта на S2, почему нет значения таймера для оставшегося возраста в минутах, когда было сконфигурировано динамическое обучение - sticky? **Так как эти адреса сохранены в конфигурационном файле,  то после перезагрузки коммутатора, их не надо заново перенастраивать.**
2.	Что касается безопасности порта на S2, если вы загружаете скрипт текущей конфигурации на S2, почему порту 18 на PC-B никогда не получит IP-адрес через DHCP? **Не понял суть вопроса.**
3.	Что касается безопасности порта, в чем разница между типом абсолютного устаревания и типом устаревание по неактивности? В** том при абсолютном устаревании не имеет значение время неактивности. И порт будет сброшен в любом случае.**

