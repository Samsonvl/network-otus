# Лабораторная работа. Поиск и устранение неполадок в работе EtherChannel

Топология

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/Topology.png)

Таблица адресации

| Устройство | Интерфейс | IP-адрес     | Маска подсети |
| ---------- | --------- | ------------ | ------------- |
| S1         | VLAN 99   | 192.168.1.11 | 255.255.255.0 |
| S2         | VLAN 99   | 192.168.1.12 | 255.255.255.0 |
| S3         | VLAN 99   | 192.168.1.13 | 255.255.255.0 |
| PC-A       | NIC       | 192.168.0.2  | 255.255.255.0 |
| PC-C       | NIC       | 192.168.0.3  | 255.255.255.0 |

Назначения сети VLAN

| VLAN | Имя        |
| ---- | ---------- |
| 10   | User       |
| 99   | Management |

Задачи

1. [Построение сети и загрузка настроек устройств](#S1)

2. [Отладка EtherChannel](#S2)

   

## Построение сети и настройка оборудования<a name="S1"></a>

Конфигурация S1

```
hostname S1
interface range f0/1-24, g0/1-2
shutdown
exit
enable secret class
no ip domain lookup
line vty 0 15
password cisco
login
line con 0
 password cisco
 logging synchronous
 login
 exit
vlan 10
 name User
vlan 99
 Name Management
interface range f0/1-2
 switchport mode trunk
 channel-group 1 mode active
 switchport trunk native vlan 99
 no shutdown
interface range f0/3-4
 channel-group 2 mode desirable
 switchport trunk native vlan 99
 no shutdown
interface f0/6
 switchport mode access
 switchport access vlan 10
 no shutdown
interface vlan 99
 ip address 192.168.1.11 255.255.255.0
interface port-channel 1
 switchport trunk native vlan 99
 switchport mode trunk
interface port-channel 2
 switchport trunk native vlan 99
 switchport mode access
```

Конфигурация S2

```
hostname S2
interface range f0/1-24, g0/1-2
 shutdown
 exit
enable secret class
no ip domain lookup
line vty 0 15
 password cisco
 login
line con 0
 password cisco
 logging synchronous
 login
 exit
vlan 10
 name User
vlan 99
 name Management
spanning-tree vlan 1,10,99 root primary
interface range f0/1-2
 switchport mode trunk
 channel-group 1 mode desirable
 switchport trunk native vlan 99
 no shutdown
interface range f0/3-4
 switchport mode trunk
 channel-group 3 mode desirable
 switchport trunk native vlan 99
interface vlan 99
 ip address 192.168.1.12 255.255.255.0
interface port-channel 1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 1,99
interface port-channel 3
 switchport trunk native vlan 99
 switchport trunk allowed vlan 1,10,99
 switchport mode trunk
```

Конфигурация S3

```
hostname S3
interface range f0/1-24, g0/1-2
 shutdown
 exit
enable secret class
no ip domain lookup
line vty 0 15
 password cisco
 login
line con 0
 password cisco
 logging synchronous
 login
 exit
vlan 10
 name User
vlan 99
 name Management
interface range f0/1-2
interface range f0/3-4
 switchport mode trunk
 channel-group 3 mode desirable
 switchport trunk native vlan 99
 no shutdown
interface f0/18
 switchport mode access
 switchport access vlan 10
 no shutdown
interface vlan 99
 ip address 192.168.1.13 255.255.255.0
interface port-channel 3
 switchport trunk native vlan 99
 switchport mode trunk
```

## Поиск и устранение неисправностей в работе EtherChannel<a name="S2"></a>

#### Выполним поиск и устранение неполадок в работе маршрутизатора S1.

Используем команду **show interfaces trunk**, чтобы убедиться в том, что агрегированные каналы работают, как транковые порты

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.1.png)

Видно что интерфейсы f0/3-4 в режиме транка

Используем команду **show etherchannel summary**, чтобы убедиться в том, что интерфейсы входят в состав соответствующего агрегированного канала, применен правильный протокол и интерфейсы задействованы

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.1b.png)

Видно что Port-channel 1 находится в дауне, об это говорим флажок `D` . Так же работает он по протоколу LACP. А интерфейсы f0/3-4 в автономном режиме

Используем команду **show run | begin interface Port-channel** для просмотра текущей конфигурации, начиная с первого интерфейса агрегированного канала.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.1c.png)

Исходя из данный видим что интерфейсы f0/3-4 в статусе `active`  и Port-channel 2 в статусе `access`. К остальному вопросов нет

Решение для S1

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.1d.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.1dd.png)

Используйте команду **show interfaces trunk** для проверки настроек транковой связи.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.1e.png)

Port-channel 1 - 2 Работают в транковом режиме

Используем команду **show etherchannel summary**, чтобы убедиться в том, что агрегированные каналы работают и задействованы

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.1f.png)

Как видим Port-channel 1/2 настроены правильно

#### Выполним поиск и устранение неполадок в работе маршрутизатора S2

Используем команду **show interfaces trunk**, чтобы убедиться в том, что агрегированные каналы работают, как транковые порты

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.2a.png)

Видим тут не хватает VLAN 10

Используем команду **show etherchannel summary**, чтобы убедиться в том, что интерфейсы входят в состав соответствующего агрегированного канала, применен правильный протокол и интерфейсы задействованы

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.2b.png)

Port-channel 3 находится в дауне

Используем команду **show run | begin interface Port-channel** для просмотра текущей конфигурации, начиная с первого интерфейса агрегированного канала.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.2c.png)

Можно уже точно сказать что F0/1-2 выключен, а на port-channel 1 не хватает VLAN 10

Решение для S2

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.2d.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.2dd.png)

Используйте команду **show interfaces trunk** для проверки настроек транковой связи.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.2e.png)

Используем команду **show etherchannel summary**, чтобы убедиться в том, что агрегированные каналы работают и задействованы

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.2f.png)

#### Выполним поиск и устранение неполадок в работе маршрутизатора S3

Используем команду **show interfaces trunk**, чтобы убедиться в том, что агрегированные каналы работают, как транковые порты

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.3a.png)

Не хватает Po2

Используем команду **show etherchannel summary**, чтобы убедиться в том, что интерфейсы входят в состав соответствующего агрегированного канала, применен правильный протокол и интерфейсы задействованы

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.3b.png)

Опять же видно что не Po2

Используем команду **show run | begin interface Port-channel** для просмотра текущей конфигурации, начиная с первого интерфейса агрегированного канала.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.3c.png)

Интерфейсы Fa0/3-4 находятся не в том Po, а F0/1-2 вообще не задействованы 

Решение для S3

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.3d.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.3dd.png)

Используйте команду **show interfaces trunk** для проверки настроек транковой связи. 

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.3e.png)

Используем команду **show etherchannel summary**, чтобы убедиться в том, что агрегированные каналы работают и задействованы

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.3f.png)

Проверим EtherChannel и подключения

Выполним эхо-запрос от коммутатора S1 на коммутатор S2

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.4%20s1%20ping%20s2.png)

Выполним эхо-запрос от коммутатора S1 на коммутатор S3

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.4%20s1%20ping%20s3.png)

Выполним эхо-запрос от коммутатора S2 на коммутатор S3

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.4%20s2%20ping%20s3.png)

Выполним эхо-запрос от ПК 1 на ПК 3

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab05/Screenshots/2.4%20pc-a%20ping%20pc-c.png)


