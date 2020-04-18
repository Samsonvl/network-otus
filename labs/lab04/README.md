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

## Настройка основных параметров коммутатора

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

## Настройка протокола PAgP

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


