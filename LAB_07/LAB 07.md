# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

**Выполнил Давыдкин Д.Г.**

### Топология

![Image]()

### Таблица адресации

|Устройство   | Интерфейс | IP-адрес  | Маска подсети  |
| ------------ | ------------ | ------------ | ------------ |
|  S1 |  VLAN 1 | 192.168.1.1  | 255.255.255.0  |
| S2  | VLAN 1  |192.168.1.1   | 255.255.255.0  |
| S3  |VLAN 1  | 192.168.1.1  | 255.255.255.0  |

### Цели

Часть 1. Создание сети и настройка основных параметров устройства
Часть 2. Выбор корневого моста
Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

### Часть 1	:Создание сети и настройка основных параметров устройства

*Шаг 1:	Создайте сеть согласно топологии.*

![Image]()

Шаг 2:	Выполните инициализацию и перезагрузку коммутаторов.

Switch>  
Switch>en  
Switch#erase startup-config   
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]y  
Switch#reload
Proceed with reload? [confirm]y

*Шаг 3:	Настройте базовые параметры каждого коммутатора.*

**Настройка S1**

Switch>  
Switch>en  
Switch#clock set 20:00:00 june 18 2022  
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
S1(config)#int vlan 1  
S1(config-if)#ip address 192.168.1.1 255.255.255.0  
S1(config-if)#no shu  
S1(config-if)#do wr  
Building configuration...  
[OK]  

**Настройка S2**

Switch>  
Switch>en  
Switch#clock set 20:05:00 june 18 2022  
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
S2(config)#int vlan 1  
S2(config-if)#ip address 192.168.1.2 255.255.255.0  
S2(config-if)#no shu  
S2(config-if)#do wr  
Building configuration...  
[OK]  

**Настройка S3**

Switch>  
Switch>en  
Switch#clock set 20:10:00 june 18 2022  
Switch#conf t  
Switch(config)#hostname S3  
S3(config)#no ip domain lookup  
S3(config)#enable secret class  
S3(config)#service password-encryption  
S3(config)#banner motd ###ENTRY CLOSED!!!###  
S3(config)#line con 0  
S3(config-line)#logging synchronous  
S3(config-line)#password cisco  
S3(config-line)#login  
S3(config-line)#line vty 0 15  
S3(config-line)#logging synchronous  
S3(config-line)#password cisco  
S3(config-line)#login  
S3(config)#int vlan 1  
S3(config-if)#ip address 192.168.1.3 255.255.255.0  
S3(config-if)#no shu  
S3(config-if)#do wr  
Building configuration...  
[OK]  

*Шаг 4:	Проверьте связь.*

*Проверьте способность компьютеров обмениваться эхо-запросами.*

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?	 **Да**

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?	**Да**

Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3?	**Да**

### Часть 2:	Определение корневого моста

*Шаг 1:	Отключите все порты на коммутаторах.*
