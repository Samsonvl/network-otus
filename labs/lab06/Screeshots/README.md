# Лабораторная работа. Настройка базового протокола OSPFv2 для одной области

Топология

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/Топология.png)

Таблица адресации

| Устройство | Интерфейс | IP-адрес     | Маска подсети   | Шлюз по умолчанию |
| ---------- | --------- | ------------ | --------------- | ----------------- |
| R1         | e0/0      | 192.168.1.1  | 255.255.255.0   | -                 |
|            | s1/0      | 192.168.12.1 | 255.255.255.252 | -                 |
|            | s1/2      | 192.168.13.1 | 255.255.255.252 | -                 |
| R2         | e0/0      | 192.168.2.1  | 255.255.255.0   | -                 |
|            | s1/2      | 192.168.12.2 | 255.255.255.252 | -                 |
|            | s1/1      | 192.168.23.1 | 255.255.255.252 | -                 |
| R3         | e0/0      | 192.168.3.1  | 255.255.255.0   | -                 |
|            | s1/0      | 192.168.13.2 | 255.255.255.252 | -                 |
|            | s1/1      | 192.168.23.2 | 255.255.255.252 | -                 |
| PC-A       | NIC       | 192.168.1.3  | 255.255.255.0   | 192.168.1.1       |
| PC-B       | NIC       | 192.168.2.3  | 255.255.255.0   | 192.168.2.1       |
| PC-C       | NIC       | 192.168.3.3  | 255.255.255.0   | 192.168.3.1       |

Задачи

1. [Создание сети и настройка основных параметров устройства](#OSPF1)
2. [Настройка и проверка маршрутизации OSPF](#OSPF2)
3. [Изменение назначений идентификаторов маршрутизаторов](#OSPF3)
4. [Настройка пассивных интерфейсов OSPF](#OSPF4)
5. [Изменение метрик OSPF](#OSPF5)

## Создание сети и настройка основных параметров устройства<a name="OSPF1"></a>

1. Создадим сеть согласно топологии

2. Выполним запуск и перезапуск маршрутизаторов

3. Произведем базовую настройку маршрутизаторов

   Пример настройки

   ```
   (config)#no ip domain-lookup 
   (config)#hostname R1 
   (config)#interface e0/0
   (config-if)#ip address 192.168.1.1 255.255.255.0
   (config-if)#no shutdown
   (config-if)#exit
   (config)#interface s1/0
   (config-if)#ip address 192.168.12.1 255.255.255.252
   (config-if)#no shutdown  
   (config-if)#exit
   (config)#interface s1/2
   (config-if)#ip address 192.168.13.1 255.255.255.252
   (config-if)#no shutdown  
   (config-if)#exit
   (config)#enable secret class
   (config)#line console 0
   (config-line)#password cisco
   (config-line)#login
   (config-line)#logging synchronous
   (config-line)#exit
   (config)#line vty 0 15
   (config-line)#password cisco
   (config-line)#login
   (config-line)#^Z
   #copy running-config startup-config
   ```

4. Настроим узлы ПК

5. Проверим связь

   R1
   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/1.5R1.png)

   R2

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/1.5R2.png)

   R3

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/1.5R3.png)

