# Лабораторная работа - Настройка протоколов CDP, LLDP и NTP

**Выполнил Давыдкин Д.Г.**

------------
### Топология

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_13/Images/Topology.jpg)


------------

### Таблица адресации

| Устройство  | Интерфейс  | IP-адрес  |  Маска подсети | Шлюз по умолчанию  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
|  R1 | Loopback1  | 172.16.1.1  |255.255.255.0   | -  |
| R1  | G0/0/1  | 10.22.0.1  |  255.255.255.0 |  - |
|  S1 |  SVI VLAN 1  | 10.22.0.2  | 255.255.255.0  | 10.22.0.1 |
|S2   | SVI VLAN 1   | 10.22.0.3 | 255.255.255.0  | 10.22.0.1 |


------------

### Задачи
Часть 1. Создание сети и настройка основных параметров устройства
Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP
Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP
Часть 4. Настройка и проверка NTP


------------
### Часть 1. Создание сети и настройка основных параметров устройства

*Шаг 1. Создайте сеть согласно топологии.*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_13/Images/Network%20PT.jpg)

*Шаг 2. Настройте базовые параметры для маршрутизатора.*

**Настройка R1**

Router>en  
Router#clock set 20:00:00 june 23 2022  
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
R1(config-line)#int g0/0/1  
R1(config-if)#ip address 10.22.0.1 255.255.255.0  
R1(config-if)#no shu  
R1(config-if)#int Lo1  
R1(config-if)#ip address 172.16.1.1 255.255.255.0  
R1(config-if)#do wr

*Шаг 3. Настройте базовые параметры каждого коммутатора.*

**Настройка S1**

Switch>  
Switch>en  
Switch#clock set 20:15:00 june 23 2022  
Switch#conf t  
Switch(config)#hostname S1  
S1(config)#no ip domain-lookup  
S1(config)#enable secret class  
S1(config)#service password-encryption  
S1(config)#banner motd ###ENTRY CLOSED!!!###  
S1(config)#line con 0  
S1(config-line)#logging synchronous  
S1(config-line)#password cisco  
S1config-line)#login  
S1(config-line)#line vty 0 15  
S1(config-line)#logging synchronous  
S1(config-line)#password cisco  
S1(config-line)#login  
S1(config-line)#int vlan 1  
S1(config-if)#ip address 10.22.0.2 255.255.255.0  
S1(config-if)#no shu  
S1(config-if)#int ra fa0/2-4, fa0/6-24, g0/1-2  
S1(config-if-range)#shu  
S1(config-line)#do wr  
Building configuration...  

**Настройка S1**

Switch>  
Switch>en  
Switch#clock set 20:15:00 june 23 2022  
Switch#conf t  
Switch(config)#hostname S1  
S2(config)#no ip domain-lookup  
S2(config)#enable secret class  
S2(config)#service password-encryption  
S2(config)#banner motd ###ENTRY CLOSED!!!###  
S2(config)#line con 0  
S2(config-line)#logging synchronous  
S2(config-line)#password cisco  
S2config-line)#2ogin  
S2(config-line)#line vty 0 15  
S2(config-line)#logging synchronous  
S2(config-line)#password cisco  
S2(config-line)#login  
S2(config-line)#int vlan 1  
S2(config-if)#ip address 10.22.0.2 255.255.255.0  
S2(config-if)#no shu   
S2(config-line)#int r fa0/2-24, g0/1-2  
S2(config-if-range)#shu
S2(config-line)#do wr  
Building configuration...  


------------


### Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP

a.	На R1 используйте соответствующую команду show cdp, чтобы определить, сколько интерфейсов включено CDP, сколько из них включено и сколько отключено.

**!~show cdp не показывает количество интерфейсов.
Воспользовался show cdp interface**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_13/Images/show%20CDP%20interface%20R1.jpg)

b.	На R1 используйте соответствующую команду show cdp, чтобы определить версию IOS, используемую на S1.

R1 # show cdp entry  S1 

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_13/Images/show%20cdp%20entry%20%20S1.jpg)

Какая версия IOS используется на  S1? ** Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)**

d.	Настройте SVI для VLAN 1 на S1 и S2, используя IP-адреса, указанные в таблице адресации выше. Настройте шлюз по умолчанию для каждого коммутатора на основе таблицы адресов.

S1(config)#int vlan 1  
S1(config-if)#ip address 10.22.0.2 255.255.255.0  
S1(config-if)#no shu  
S1(config-if)#exit  
S1(config)#ip default-gateway 10.22.0.1  

S2(config)#int vlan 1  
S2(config-if)#ip address 10.22.0.3 255.255.255.0  
S2(config-if)#no shu  
S2(config-if)#exit  
S2(config)#ip default-gateway 10.22.0.1  


e.	На R1 выполните команду show cdp entry S1 . 

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_13/Images/show%20cdp%20entry%20%20S1.jpg)

Вопрос:
Какие дополнительные сведения доступны теперь?

**Entry address(es):   
  IP address : 10.22.0.2  
  Появился адрес, который настроен на интерфейса VLAN 1.**

f.	Отключить CDP глобально на всех устройствах. 

R1(config)#no cdp run 

S1(config)#no cdp run 

S2(config)#no cdp run 

R1#show cdp  
% CDP is not enabled  


------------


### Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP

R1(config)#lldp run 

S1(config)#lldp run

S2(config)#lldp run

b.	На S1 выполните соответствующую команду lldp, чтобы предоставить подробную информацию о S2. 

**S1# show lldp entry S2** 

**~!Отсутствует в CPT Entry, доступно только neighbours.**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_13/Images/show%20lldp%20neighbours%20S1.jpg)

Что такое chassis ID  для коммутатора S2? **MAC-адрес интерфейса через который коммутатор S2 подключен к S1.**

c.	Соединитесь через консоль на всех устройствах и используйте команды LLDP, необходимые для отображения топологии физической сети только из выходных данных команды show

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_13/Images/show%20LLDP%20PC.jpg)

------------

### Часть 4. Настройка NTP
*
Шаг 1. Выведите на экран текущее время.*

Откройте окно конфигурации  
Введите команду show clock для отображения текущего времени на R1. Запишите   отображаемые сведения о текущем времени в следующей таблице. 

| Дата  | Время  | Часовой пояс  | Источник времени  |
| ------------ | ------------ | ------------ | ------------ |
| Jun 24 2022  |  20:53:58.194 |  UTC | ??? Ручная настройка clock set  |

*Шаг 2. Установите время.*
С помощью команды clock set установите время на маршрутизаторе R1. Введенное время должно быть в формате UTC. 

R1#clock set 22:00:00 june 23 2022

*Шаг 3. Настройте главный сервер NTP.*

R1(config)#ntp master 4
R1(config)#ntp server 10.22.0.1

*Шаг 4. Настройте клиент NTP.*

a.	Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время. Запишите текущее время,  в следующей таблице.

| Дата  | Время  | Часовой пояс  | Источник времени  |
| ------------ | ------------ | ------------ | ------------ |
| Jun 24 2022   |  21: 7:39.74 | UTC  |  ??? Ручная настройка clock set  |
| Jun 24 2022  | 21: 9:11.451  | UTC  |  ??? Ручная настройка clock set  |

b.	Настройте S1 и S2 в качестве клиентов NTP. Используйте соответствующие команды NTP для получения времени от интерфейса G0/0/1 R1, а также для периодического обновления календаря или аппаратных часов коммутатора.

S1(config)#ntp server 10.22.0.1

S2(config)#ntp server 10.22.0.1

*Шаг 5. Проверьте настройку NTP.*

a.	Используйте соответствующую команду show , чтобы убедиться, что S1 и S2 синхронизированы с R1.
Примечание. Синхронизация метки времени на маршрутизаторе R2 с меткой времени на маршрутизаторе R1 может занять несколько минут.
b.	Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время и сравнить ранее записанное время.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_13/Images/show%20clock%20NTP%20.jpg)

Вопрос для повторения
Для каких интерфейсов в пределах сети не следует использовать протоколы обнаружения сетевых ресурсов? Поясните ответ.
