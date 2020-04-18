# Лабораторная работа. Настройка EtherChannel

Топология
![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/topology.png)



Таблица адресации

| Устройство | Интерфейс | IP-адрес      | Маска подсети |
| ---------- | --------- | ------------- | ------------- |
| S1         | VLAN 99   | 192.168.99.11 | 255.255.255.0 |
| S2         | VLAN 99   | 192.168.99.12 | 255.255.255.0 |
| S3         | VLAN 99   | 192.168.99.13 | 255.255.255.0 |
| PC-A       | NIC       | 192.168.10.1  | 255.255.255.0 |
| PC-B       | NIC       | 192.168.10.2  | 255.255.255.0 |
| PC-C       | NIC       | 192.168.10.3  | 255.255.255.0 |

Задачи

1. [Выполнить базовую настройку коммутаторов](#EC1)
2. [Настройка PAgP](#EC2)
3. [Настройка LACP](#EC3)

## Настройка основных параметров коммутатора<a name="EC1"></a>

Ниже пример, с полной конфигурацией можно ознакомиться [тут]()

```
(config)#no ip domain-lookup
(config)#hostname S1
(config)#enable secret class
(config)#line console 0
(config-line)#password cisco
(config-line)#login
(config-line)#logging synchronous
(config-line)#exit
(config)#line vty 0 15
(config-line)#password cisco
(config-line)#login
(config-line)#exit
(config)#interface range fastEthernet 0/1 - 14, fa0/16 - 24, g0/1 - 2
(config-if-range)#shutdown
(config-if-range)#exit
(config)#vlan 99
(config-vlan)#name Management
(config-vlan)#exit
(config)#vlan 10
(config-vlan)#name Staff
(config-vlan)#exit
(config)#int f0/15
(config-if)#switchport mode access
(config-if)#switchport access vlan 10
(config-if)#exit
(config)#int vlan 99
(config-if)#ip address 192.168.99.11 255.255.255.0
(config-if)#no shut
```

## Настройка протокола PAgP<a name="EC2"></a>

##### Настроим PAgP на S1 и на S3

S1

```
S1(config)# interface range f0/1 - 2
S1(config-if-range)# channel-group 1 mode desirable
Creating a port-channel interface Port-channel 1

S1(config-if-range)# no shutdown
```

S3

```
S3(config)# interface range f0/3 - 4
S3(config-if-range)# channel-group 1 mode auto
Creating a port-channel interface Port-channel 1

S3(config-if-range)# no shutdown
```

##### Проверим конфигурацию на портах

В настоящее время интерфейсы F0/1, F0/2 и Po1 (Port-channel1) на коммутаторах S1 и S3 находятся в режиме доступе, а режим управления установлен на динамический автоматический режим (dynamic auto). Проверим конфигурацию с помощью соответствующих команд **show run interface** 

![Screenshot_4](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_4.png)

##### Убедимся что порты объединены

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_3.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_5.png)

Здесь видно группу port-channel, используемый протокол, интерфейсы и их состояние. В данном случае параметр SU говорит о том, что выполнено агрегирование второго уровня и то, что этот интерфейс используется. А параметр P указывает, что интерфейсы в состоянии port-channel.

##### Настроим транковые порты

После агрегирования портов команды, применённые на интерфейсе Port Channel, влияют на все объединённые в группу каналы. Вручную настроим порты Po1 на S1 и S3 в качестве транковых и назначьте их сети native VLAN 99.

S1

```
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 99
```

S3

```
S3(config)# interface port-channel 1
S3(config-if)# switchport mode trunk
S3(config-if)# switchport trunk native vlan 99
```

##### Убедимся в том, что порты настроены в качестве транковых

Выполним команды **show run** **interface** *идентификатор-интерфейса* на S1 и S3

![S1](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_6.png)

![S3](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_7.png)
Как видно настройки port-channel 1 применились и на 2 интерфейса и она стали транковыми

Выполним команды **show interfaces trunk** и **show spanning-tree** на S1 и S3.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_9.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_11.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_12.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_13.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_14.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_15.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_16.png)

На основе этих данных можно сделать вывод что Native VLAN 99. Значение стоимости 12 и приоритет 128

## Настройка протокола LACP<a name="EC3"></a>

##### Настроим LACP между S1 и S2

S1

```
S1(config)# interface range f0/1-2
S1(config-if-range)# switchport mode trunk
S1(config-if-range)# switchport trunk native vlan 99
S1(config-if-range)# channel-group 2 mode active
Creating a port-channel interface Port-channel 2

S1(config-if-range)# no shutdown
```

S2

```
S2(config)# interface range f0/1-2
S2(config-if-range)# switchport mode trunk
S2(config-if-range)# switchport trunk native vlan 99
S2(config-if-range)# channel-group 2 mode passive
Creating a port-channel interface Port-channel 2

S2(config-if-range)# no shutdown
```

##### Убедимся что порты объединены

![S1](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_16.png)

![S2](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_17.png)

Здесь видно группу port-channel, используемый протокол, интерфейсы и их состояние. В данном случае параметр SU говорит о том, что выполнено агрегирование второго уровня и то, что этот интерфейс используется. А параметр P указывает, что интерфейсы в состоянии port-channel.

##### Настроим LACP между S2 и S3

```
S2(config)# interface range f0/3-4
S2(config-if-range)# switchport mode trunk
S2(config-if-range)# switchport trunk native vlan 99
S2(config-if-range)# channel-group 3 mode active
Creating a port-channel interface Port-channel 3
S2(config-if-range)# no shutdown
```

```
S3(config)# interface range f0/1-2
S3(config-if-range)# switchport mode trunk
S3(config-if-range)# switchport trunk native vlan 99
S3(config-if-range)# channel-group 3 mode passive
Creating a port-channel interface Port-channel 3
S3(config-if-range)# no shutdown
```

##### Убедимся что порты объединены

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_18.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_19.png)

Как видим EtherChannel образован

##### Проверим наличие сквозного соединения

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_20.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_22.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab04/Screenshots/Screenshot_23.png)

Как видим все пинги проходят в пределах одой сети VLAN

### Вывод

Etherchannel — это технология, позволяющая объединять (агрегировать) несколько физических проводов (каналов, портов) в единый логический интерфейс. Как правило, это используется для повышения отказоустойчивости и увеличения пропускной способности канала. Обычно, для соединения критически важных узлов (коммутатор-коммутатор, коммутатор-сервер и др.). Само слово Etherchannel введено компанией Cisco и все, что связано с агрегированием, она включает в него. В принципе отличий PAgP от LACP практически никаких, кроме самой структуры.
