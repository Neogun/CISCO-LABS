# Лабораторная работа - Настройка NAT для IPv4

**Выполнил Давыдкин Д.Г.**

### Топология

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_12/Images/Topology.jpg)


------------

### Таблица адресации

| Устройство  | Интерфейс  | IP-адрес  | Маска подсети   |
| ------------ | ------------ | ------------ | ------------ |
| R1  |G0/0/0   | 209.165.200.230  | 255.255.255.248  |
|  R1 |G0/0/1   | 192.168.1.1  |255.255.255.0   |
|R2   |G0/0/0   |209.165.200.225   | 255.255.255.248  |
| R2  | Lo1  | 209.165.200.1  |255.255.255.224  |
| S1  | VLAN 1  | 192.168.1.11  |255.255.255.0   |
|  S2 |VLAN 1   | 192.168.1.12  |  255.255.255.0 |
| PC-A  | NIC  |  192.168.1.2 | 255.255.255.0  |
|PC-B   |  NIC |192.168.1.3   | 255.255.255.0  |


------------
### Цели 

Часть 1. Создание сети и настройка основных параметров устройства
Часть 2. Настройка и проверка NAT для IPv4
Часть 3. Настройка и проверка PAT для IPv4
Часть 4. Настройка и проверка статического NAT для IPv4.


------------

### Часть 1. Создание сети и настройка основных параметров устройства

*Шаг 1. Подключите кабели сети согласно приведенной топологии.*

![IMAGE](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_12/Images/Network%20PT.jpg)


*Шаг 2. Произведите базовую настройку маршрутизаторов.*

**Настройка R1 **

Router>en  
Router#clock set 18:00:00 june 23 2022  
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
R1(config-line)#int g0/0/0  
R1(config-if)#ip address 209.165.200.230 255.255.255.248  
R1(config-if)#no shu  
R1(config-if)#int g0/0/1  
R1(config-if)#ip address 192.168.1.1 255.255.255.0 
R1(config-if)#no shu  
R1(config-if)#exit  
R1(config)#ip route 0.0.0.0 0.0.0.0 209.165.200.225  
R1(config-if)#do wr  

**Настройка R2**

Router>en  
Router#clock set 18:10:00 june 23 2022  
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
R2(config)#int g0/0/0  
R2(config-if)#ip address 209.165.200.225 255.255.255.248  
R2(config-if)#no shu  
R2(config-if)#int Lo1  
R2(config-if)#ip address 209.165.200.1 255.255.255.224  
R2(config)#ip route 0.0.0.0 0.0.0.0 209.165.200.230  
R2(config-if)#do wr   

*Шаг 3. Настройте базовые параметры каждого коммутатора.*

**Настройка S1**

Switch>  
Switch>en  
Switch#clock set 18:30:00 june 23 2022  
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
S1(config-line)#int  vlan 1  
S1(config-if)#ip address192.168.1.11 255.255.255.0  
S1(config-if)#no shu  
S1(config-if)#int r fa0/2-4, fa0/7-24, g0/1-2  
S1(config-if-range)#shu  
Building configuration...  
[OK]  

**Настройка S2**

Switch>  
Switch>en  
Switch#clock set 18:40:00 june 23 2022  
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
S2(config-line)#int vlan 1
S2(config-if)#ip address 192.168.1.12 255.255.255.0
S2(config-if)#no shu
S2(config-if)#int r fa0/2-17, fa0/19-24, g0/1-2  
S2(config-if-range)#shu  
Building configuration...  
[OK]  

### Часть 2. Настройка и проверка NAT для IPv4.

*Шаг 1. Настройте NAT на R1, используя пул из трех адресов 209.165.200.226-209.165.200.228. *

a.	Настройте простой список доступа, который определяет, какие хосты   будут разрешены для трансляции. В этом случае все устройства в   локальной сети R1 имеют право на трансляцию.  
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255   

b.	Создайте пул NAT и укажите ему имя и диапазон используемых адресов.  
R1(config)# ip nat pool PUBLIC_ACCESS 209.165.200.226 209.165.200.228     netmask 255.255.255.248   

c.	Настройте перевод, связывая ACL и пул с процессом преобразования.
R1(config)# ip nat inside source list 1 pool PUBLIC_ACCESS 

d.	Задайте внутренний (inside) интерфейс.   
R1(config)# interface g0/0/1  
R1(config-if)# ip nat inside  

e.	Определите внешний (outside) интерфейс.  
R1(config)# interface g0/0/0  
R1(config-if)# ip nat outside  

*Шаг 2. Проверьте и проверьте конфигурацию.   *

a.	С PC-B,  запустите эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните процес поиска и устранения неполадок. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.

![Image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_12/Images/ping%20PC-B%20to%20Lo1%20R2.jpg)


**!~ В Cisco Packet Tracer команда присутствует, но у меня маршрутизатор не реагировал на неё.**

R1# show ip nat translations
Pro Inside global Inside local Outside local Outside global
--- 209.165.200.226 192.168.1.3 --- --- 
226:1 192.168.1. 3:1 209.165.200. 1:1 209.165.200. 1:1 
Total number of translations: 2

Во что был транслирован внутренний локальный адрес PC-B? **В первый свободный адрес статического NAT.**

Какой тип адреса NAT является переведенным адресом? **Адрес из динамического пула.**

b.	С PC-A, запустите  эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните отладку. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.

![image](https://github.com/Neogun/CISCO-LABS/blob/main/LAB_12/Images/ping%20PC-A%20to%20Lo1%20R2.jpg)

**!~ В Cisco Packet Tracer команда присутствует, но у меня маршрутизатор не реагировал на неё.**

R1# show ip nat translations   
Pro Inside global Inside local Outside local Outside global  
--- 209.165.200.227 192.168.1.2 --- ---  
--- 209.165.200.226 192.168.1.3 --- ---  
227:1 192.168.1. 2:1 209.165.200. 1:1 209.165.200. 1:1  
226:1 192.168.1. 3:1 209.165.200. 1:1 209.165.200. 1:1  
Total number of translations: 4  

c.	 На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.

**!~ В Cisco Packet Tracer команда присутствует, но у меня маршрутизатор не реагировал на неё.** 

R1# show ip nat translations  
Pro Inside global Inside local Outside local Outside global  
--- 209.165.200.227 192.168.1.2 --- ---  
--- 209.165.200.226 192.168.1.3 --- ---  
--- 209.165.200.228 192.168.1.11 --- ---  
226:1 192.168.1. 3:1 209.165.200. 1:1 209.165.200. 1:1  
228:0 192.168.1. 11:0 209.165.200. 1:0 209.165.200. 1:0 209.165.200. 1:0  
Total number of translations: 5  


d.	Теперь запускаем пинг R2 Lo1 из S2. На этот раз перевод завершается неудачей, и вы получаете эти сообщения (или аналогичные) на консоли R1:

Sep 23 15:43:55.562: %IOSXE-6-PLATFORM: R0/0: cpp_cp: QFP:0.0 Thread:000   TS:00000001473688385900 %NAT-6-ADDR_ALLOC_FAILURE: Address   allocation failed; pool 1 may be exhausted   

e.	Это ожидаемый результат, потому что выделено только 3 адреса, и мы попытались ping Lo1 с четырех устройств. Напомним, что NAT — это трансляция «один-в-один». Как много выделено трансляций? Введите команду show ip nat translations verbose , и вы увидите, что ответ будет 24 часа.

R1# show ip nat translations verbose   
Pro Inside global Inside local Outside local Outside global  
--- 209.165.200.226 192.168.1.3 --- ---  
  create: 09/23/19 15:35:27, use: 09/23/19 15:35:27, timeout: 23:56:42  
  Map-Id(In): 1  
<output omitted>  

f.	Учитывая, что пул ограничен тремя адресами, NAT для пула адресов недостаточно для нашего приложения. Очистите преобразование NAT и статистику, и мы перейдем к PAT. 

R1# clear ip nat translations * 

R1# clear ip nat statistics    **!~Отсутствует в CPT**


------------
### Часть 3. Настройка и проверка PAT для IPv4.

*Шаг 1. Удалите команду преобразования на R1.*

R1(config)# no ip nat inside source list 1 pool PUBLIC_ACCESS 

*Шаг 2. Добавьте команду PAT на R1.*

ip nat inside source list 1 pool PUBLIC_ACCESS overload  

*Шаг 3. Протестируйте и проверьте конфигурацию.*

a.	Давайте проверим, что PAT работает. С PC-B,  запустите эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните отладку. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.

R1# show ip nat translations  
Pro Inside global Inside local Outside local Outside global  
226:1 192.168.1. 3:1 209.165.200. 1:1 209.165.200. 1:1  
Total number of translations: 1#  
 
 Во что был транслирован внутренний локальный адрес PC-B?
 
Какой тип адреса NAT является переведенным адресом?

Чем отличаются выходные данные команды show ip nat translations из упражнения NAT?

b.	С PC-A, запустите эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните отладку. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.

R1# show ip nat translations  
Pro Inside global Inside local Outside local Outside global  
226:1 192.168.1. 2:1 209.165.200. 1:1 209.165.200. 1:1  
Total number of translations: 1  

Обратите внимание, что есть только одна трансляция. Отправьте ping еще раз, и быстро вернитесь к маршрутизатору и введите команду show ip nat translations verbose , и вы увидите, что произошло.
  
R1# show ip nat translations verbose   
Pro Inside global Inside local Outside local Outside global  
icmp 209.165.200.226:1 192.168.1.2:1 209.165.200.1:1 209.165.200.1:1   
  create: 09/23/19 16:57:22, use: 09/23/19 16:57:25, timeout: 00:01:00  
<output omitted>  


c.	Генерирует трафик с нескольких устройств для наблюдения PAT. На PC-A и PC-B используйте параметр -t с командой ping, чтобы отправить безостановочный ping на интерфейс Lo1 R2 (ping -t 209.165.200.1), затем вернитесь к R1 и выполните команду show ip nat translations:

R1# show ip nat translations  
Pro Inside global Inside local Outside local Outside global  
icmp 209.165.200.226:1 192.168.1.2:1 209.165.200.1:1 209.165.200.1:1   
226:2 192.168.1. 3:1 209.165.200. 1:1 209.165.200. 1:2   
Total number of translations: 2   

Как маршрутизатор отслеживает, куда идут ответы? **  С помощью назначения разные подключениям разных портов.**

d.	PAT в пул является очень эффективным решением для малых и средних организаций. Тем не менее есть неиспользуемые адреса IPv4, задействованные в этом сценарии. Мы перейдем к PAT с перегрузкой интерфейса, чтобы устранить эту трату IPv4 адресов. Остановите ping на PC-A и PC-B с помощью комбинации клавиш Control-C, затем очистите трансляции и статистику:

R1# clear ip nat translations *   
R1# clear ip nat statistics   

*Шаг 4. На R1 удалите команды преобразования nat pool.*

R1(config)# no ip nat inside source list 1 pool PUBLIC_ACCESS overload   
R1(config)# no ip nat pool PUBLIC_ACCESS  

*Шаг 5. Добавьте команду PAT overload, указав внешний интерфейс.*

Добавьте команду PAT, которая вызовет перегрузку внешнего интерфейса.

R1(config)# ip nat inside source list 1 interface g0/0/0 overload  

*Шаг 6. Протестируйте и проверьте конфигурацию. *

a.	Давайте проверим PAT, чтобы интерфейс работал. С PC-B,  запустите эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните отладку. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.

**!~ В Cisco Packet Tracer команда присутствует, но у меня маршрутизатор не реагировал на неё.**  

R1# show ip nat translations  
Pro Inside global Inside local Outside local Outside global  
209.165.200. 230:1 192.168.1. 3:1 209.165.200. 1:1 209.165.200. 1:1   
Total number of translations: 1   

b.	Сделайте трафик с нескольких устройств для наблюдения PAT. На PC-A и PC-B используйте параметр -t с командой ping для отправки безостановочного ping на интерфейс Lo1 R2 (ping -t 209.165.200.1). На S1 и S2 выполните привилегированную команду exec ping 209.165.200.1 повторить 2000. Затем вернитесь к R1 и выполните команду show ip nat translations.

R1# show ip nat translations  
Pro Inside global Inside local Outside local Outside global  
209.165.200. 230:3 192.168.1. 11:1 209.165.200. 1:1 209.165.200. 1:3   
209.165.200. 230:2 192.168.1. 2:1 209.165.200. 1:1 209.165.200. 1:2   
209.165.200. 230:4 192.168.1. 3:1 209.165.200. 1:1 209.165.200. 1:4   
209.165.200. 230:1 192.168.1. 12:1 209.165.200. 1:1 209.165.200. 1:1   
Total number of translations: 4   


------------
### Часть 4. Настройка и проверка статического NAT для IPv4.

*Шаг 1. На R1 очистите текущие трансляции и статистику.*

R1# clear ip nat translations * 

R1# clear ip nat statistics   **!~Команду отсутствует в CPT**

*Шаг 2. На R1 настройте команду NAT, необходимую для статического сопоставления внутреннего адреса с внешним адресом.*

R1(config)# ip nat inside source static 192.168.1.2 209.165.200.229 

*Шаг 3. Протестируйте и проверьте конфигурацию.*

a.	Давайте проверим, что статический NAT работает. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations, и вы увидите статическое сопоставление.

R1# show ip nat translations  
Pro Inside global Inside local Outside local Outside global  
--- 209.165.200.229 192.168.1.2 --- ---  
Total number of translations: 1  

b.	Таблица перевода показывает, что статическое преобразование действует. Проверьте это, запустив ping  с R2 на 209.165.200.229. Плинги должны работать.  


c.	На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations, и вы увидите статическое сопоставление и преобразование на уровне порта для входящих pings .

R1# show ip nat translations  
Pro Inside global Inside local Outside local Outside global  
--- 209.165.200.229 192.168.1.2 --- ---  
229:3 192.168.1. 2:3 209.165.200. 225:3 209.165.200. 225:3 209.165.200.   
Total number of translations: 2  
**Это подтверждает, что статический NAT работает.  **

