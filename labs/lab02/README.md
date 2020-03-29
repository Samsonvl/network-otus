# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами
### Топология
![](Screenshot_1.png)

### Таблица адресации
| Устройство   | Интерфейс      | IP-адрес                 | Маска подсети       | 
|-------------:|:---------------|-------------------------:|:--------------------|
| S1           | VLAN 1         | 192.168.99.1             | 255.255.255.0       | 
| S2           | VLAN 1         | 192.168.99.2             | 255.255.255.0       | 
| S3           | VLAN 1         | 192.168.99.3             | 255.255.255.0       | 

## Цели
1. Создание сети и настройка основных параметров устройства
2. Выбор корневого моста
3. наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

## Решение
1. [После создания сети выполним настройку базовых параметров каждого коммутатора и проврим связь между ними](#1)
2. [Выбор корневого моста](#2) 
   * [Отключим все порты](#2.1)
   * [Настроим порты в качестве транковых](#2.2)
   * [Включим порты F0/2 и F0/4](#2.3)
   * [Отобразим данные протокола spanning-tree](#2.4)
3. [Для наблюдения выбора протоколом STP порта, исходя из стоимости необходимо:](#3)
   * [Опрелить на каком коммутаторе есть заблокированный порт](#3.1)
   * [Изменить стоимость порта](#3.2) 
   * [Посмотреть изменения spanning-tree](#3.3)
   * [Убрать стоимость изменения](#3.4)
4. [Для наблюдения выбора протоколом STP порта, исходя из приоритета необходимо:](#4)
   * [Включить порты F0/1 и F0/3](#4.1)
   * [Выполнить команду show spanning-tree на коммутаторах некорневого моста](#4.2)

   
## Базовая настройка<a name="1"></a>
### Пример базовой настройки для всех коммутаторов
```
Switch(config)#no ip domain-lookup // Отключает поск DNS
Switch(config)#hostname S1 //Присваиваем имя 
S1(config)#enable password class // Пароль для привилегерованного режима class
S1(config)#line console 0
S1(config-line)#password cisco // Пароль для входа через консоль
S1(config-line)#logging synchronous // Для того чтобы сообщения из консоли не мешала набирать команды
S1(config-line)#exit
S1(config)#line vty 0 15
S1(config-line)#password cisco  // Пароль на telnet
S1(config-line)#login
S1(config)#banner motd # text #  // Баннерное сообщение
S1(config)#interface vlan 1 
S1(config-if)#ip address 192.168.1.1 255.255.255.0 // Задаем ip-адрес, ориентируясь на таблицу
S1(config-if)#no shutdown
S1(config-if)#exit
S1#copy running-config startup-config // Копирует конфигурацию в файл загрузочной конфигурации
Destination filename [startup-config]? 
Building configuration...
[OK]
```
### Проверим связь (для примера S2 и S3)
```
S2#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/3/4 ms
```
## Выбор корневого моста<a name="2"></a>
Коммутатор с наименьшим значением идентификатора моста (BID) становится корневым мостом. Приоритет идентификатора моста рассчитывается путем сложения значений приоритета и расширенного идентификатора системы. У нас в примере приоритет по умолчанию = 32768, номер сети VLAN 1, следовательно, коммутатор с самым низким значением MAC-адреса становится корневым мостом. *В нашем примере это коммутатор  S1*
#### Отключим все порты<a name="2.1"></a>
```
Пример выключния порта
S1(config)#interface f0/3
S1(config-if)#shutdown
```
#### Настройка портов в качестве транковых<a name="2.2"></a>
```
Пример перевода порта в транковый режим
S1(config)#interface f0/1       
S1(config-if)#switch
S1(config-if)#switchport mode trunk
```
#### Включает F0/2 и F0/4<a name="2.3"></a>
```
Пример включения
S1(config)#interface f0/2
S1(config-if)#no shutdown
```
#### Отобразим данные spanning-tree<a name="2.4"></a>
S1 **show spanning-tree**
```
S1# 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0008.302d.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/2               Desg FWD 19        128.2    P2p 
Fa0/4               Desg FWD 19        128.4    P2p 
S1#
```
S2 **show spanning-tree**
```
S2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0015.fac1.4500
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p 
Fa0/4            Root FWD 19        128.4    P2p 
S2#
```
S3 **show spanning-tree**
```
S3#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0011.2142.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p 
Fa0/4            Root FWD 19        128.4    P2p 
```
Исходя из данных выше можно сделать следующие выводы
* Коммутатор S1 является корневым мостом так как BID у всех коммутаторов одинаковый  32769, в это случаи коммутатор с самым саленьким значением MAC-адресом становится корневым мостом.
* Корневыми портами являются на S2 это порт F0/4 и на S3 это порт F0/4
* Назначенными портами на коммутаторах являются на S1 F0/2 и F0/4 на S3 F0/2
* В качестве альтернативного и заблокированного порта является на S2 F0/2. Потому что, если на S2 F0/4 будет заблокирован, то путь до корнерого коммутатора будет проложен через этот порт
## Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
#### Определите коммутатор с заблокированным портом.
В примере ниже протокол spanning-tree блокирует порт F0/4 на коммутаторе с самым высоким идентификатором BID (S2).
```
S2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0015.fac1.4500
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p 
Fa0/4            Root FWD 19        128.4    P2p 
S2#
```
```
S1# 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0008.302d.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/2               Desg FWD 19        128.2    P2p 
Fa0/4               Desg FWD 19        128.4    P2p 
S1#
```
#### Меняем стоимость порта
```
S2(config)#interface f0/4
S2(config-if)#spanning-tree cost 18
```
#### Изменения протокола spanning-tree
Заблокированный порт теперь является назначенный и spannig-tree теперь блокирует порт на другом коммутаторе. Так произошло потому что снизилась стоимость и приоритет spannig-tree отдал ему, а другой попал в блокировку
```
S2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        18
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0015.fac1.4500
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 15 
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg LIS 19        128.2    P2p 
Fa0/4            Root FWD 18        128.4    P2p
```
```
S3#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0011.2142.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p 
Fa0/4            Root FWD 19        128.4    P2p 
S3#
```
#### Вернем изменения стоимости порта
```
S2(config)#interface f0/4
S2(config-if)#no spanning-tree cost 18
```
## Часть 4:	Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов
Для начала включим порты F0/1 и F0/3 на всех коммутаторах
```
Пример включения
S1(config)#interface f0/1
S1(config-if)#no shutdown 
```
Далее выполним команду spannig-tree на всех коммутаторах некорневого моста
```
S2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        3 (FastEthernet0/3)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0015.fac1.4500
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/1            Altn BLK 19        128.1    P2p 
Fa0/2            Altn BLK 19        128.2    P2p 
Fa0/3            Root FWD 19        128.3    P2p 
Fa0/4            Altn BLK 19        128.4    P2p 
S2#
```
```
S3#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        3 (FastEthernet0/3)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0011.2142.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/1            Desg FWD 19        128.1    P2p 
Fa0/2            Desg FWD 19        128.2    P2p 
Fa0/3            Root FWD 19        128.3    P2p 
Fa0/4            Altn BLK 19        128.4    P2p 
S3#
```
Из данных выше можно сделать следующие выводы:
* Так как стоимость портов равна и BID тоже равный, то для определения корневого моста используется приоритет портов. 
* F0/3 приоритетней чем F0/4, поэтому F0/3 был выбрать корневым мостом
