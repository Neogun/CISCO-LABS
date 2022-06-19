# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

**Выполнил Давыдкин Д.Г.**



### Топология

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_07/Images/Topology.jpg)


------------


### Таблица адресации

|Устройство   | Интерфейс | IP-адрес  | Маска подсети  |
| ------------ | ------------ | ------------ | ------------ |
|  S1 |  VLAN 1 | 192.168.1.1  | 255.255.255.0  |
| S2  | VLAN 1  |192.168.1.1   | 255.255.255.0  |
| S3  |VLAN 1  | 192.168.1.1  | 255.255.255.0  |


------------


### Цели

Часть 1. Создание сети и настройка основных параметров устройства
Часть 2. Выбор корневого моста
Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов


------------


### Часть 1	:Создание сети и настройка основных параметров устройства

*Шаг 1:	Создайте сеть согласно топологии.*

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_07/Images/Network%20PT.jpg)

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

------------



### Часть 2:	Определение корневого моста

*Шаг 1:	Отключите все порты на коммутаторах.*

S1(config)#int r fa0/1-24, gig0/1-2, vlan 1  
S1(config-if-range)#shu  

S2(config)#int r fa0/1-24, gig0/1-2, vlan 1  
S2(config-if-range)#shu  

S3(config)#int r fa0/1-24, gig0/1-2, vlan 1  
S3(config-if-range)#shu  

*Шаг 2:	Настройте подключенные порты в качестве транковых.*

S1(config)#int r fa0/1-4  
S1(config)#switchport mode trunk  

S2(config)#int r fa0/1-4  
S2(config)#switchport mode trunk   

S3(config)#int r fa0/1-4  
S3(config)#switchport mode trunk 

*Шаг 3:	Включите порты F0/2 и F0/4 на всех коммутаторах.
*
S1(config)#int r fa0/2,fa0/4  
S1(config-if-range)#no shu  

S2(config)#int r fa0/2,fa0/4  
S2(config-if-range)#no shu  

S3(config)#int r fa0/2,fa0/4
S3(config-if-range)#no shu

*Шаг 4:	Отобразите данные протокола spanning-tree.*

**show span S1**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_07/Images/show%20spanning-tree%20S1.jpg)

**show span S2**
![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_07/Images/show%20spanning-tree%20S2.jpg)

**show span S3**
![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_07/Images/show%20spanning-tree%20S3.jpg)



Схема выбора портов.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_07/Images/STP.jpg)


С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.
Какой коммутатор является корневым мостом? **Коммутатор S3.**

Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста? **Приоритет моста у всех коммутаторов равны, выбор сделан за счёт наименьшего Mac-адреса S3.   Address     000D.BD9B.E626**

Какие порты на коммутаторе являются корневыми портами? 
**На S3 корневых портов нет, так как он является корневым коммутатором. На S2 порт Fa0/4, на S1 порт Fa0/4**

Какие порты на коммутаторе являются назначенными портами? 
**Порты Fa0/2 и Fa0/4 коммутатора S3, так как он является корневым. И порт Fa0/2 коммутатора S1.**

Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? **Порт Fa0/2 коммутатора S2.**

Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта? **Это альтернативный путь до корневого коммутатора. Был выбран из-за большей метрики, так как Fa0/4 напрямую подключен к корневому.**


------------


### Часть 3:	Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

*Шаг 1:	Определите коммутатор с заблокированным портом.*

На скриншотах в предыдущей части было видно, что коммутатор с заблокированным порт был S2.

*Шаг 2:	Измените стоимость порта.*

S2(config)#int fa0/4  
S2(config-if)#spanning-tree cost 18  

*Шаг 3:	Просмотрите изменения протокола spanning-tree.*

### У меня не получилось поменять стоимость порта командой spanning-tree cost

Почему протокол spanning-tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, который был назначенным портом на другом коммутаторе? **Так как меняется стоимость пути до корневого коммутатора.**

------------



### Часть 4:	Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

*a.	Включите порты F0/1 и F0/3 на всех коммутаторах.*

S1(config)#int r fa0/1, fa0/3  
S1(config-if-range)#no shu  

S2(config)#int r fa0/1, fa0/3  
S2(config-if-range)#no shu  
 
S3(config)#int r fa0/1, fa0/3  
S3(config-if-range)#no shu  

*b.	Подождите 30 секунд, чтобы протокол STP завершил процесс перевода порта, после чего выполните команду show spanning-tree на коммутаторах некорневого моста. *

**show span S1**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_07/Images/show%20spanning-tree%20S1%20all%20ports%20enable.jpg)

**show span S2**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_07/Images/show%20spanning-tree%20S2%20all%20ports%20enable.jpg)


**show span S3**

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_07/Images/show%20spanning-tree%20S3%20all%20ports%20enable.jpg)

Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста? **На S1 выбран порт Fa0/3, на S2 выбран порт Fa0/3
**
Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах? **Так как стоимость и BID у этих портов одинакова, то выбор сделан на основе приоритета порта. Приотитет определяется числом 128.номер порта. Поэтому на обоих некорневых коммутаторах были выбраны Fa0/3 напрямую подключенные к корневому.**

------------


### 	Вопросы для повторения

1.	Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта? **Стоимость (метрику) портов. Т.е путь до корневого коммутатора.**

2.	Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта? **Будуд сделаны выборы на основе Bridge ID.**

3.	Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта? **Будет сделан выбор по приоритету порта. Порт с наименьшим индексом станет корневым портом.**



