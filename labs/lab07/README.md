# Лабораторная работа. Настройка OSPFv2 для нескольких областей

Топология

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/Топология.png)

Таблица адресации

| Устройство | Интерфейс | IP-адрес        | Маска подсети   |
| ---------- | --------- | --------------- | --------------- |
| R1         | Lo0       | 209.165.200.225 | 255.255.255.252 |
|            | Lo1       | 192.168.1.1     | 255.255.255.0   |
|            | Lo2       | 192.168.2.1     | 255.255.255.0   |
|            | s1/0      | 192.168.12.1    | 255.255.255.252 |
| R2         | Lo6       | 192.168.6.1     | 255.255.255.0   |
|            | s1/0      | 192.168.12.2    | 255.255.255.252 |
|            | s1/1      | 192.168.23.1    | 255.255.255.252 |
| R3         | Lo4       | 192.168.4.1     | 255.255.255.0   |
|            | Lo5       | 192.168.5.1     | 255.255.255.0   |
|            | S1/1      | 192.168.23.2    | 255.255.255.252 |

Задачи

1. [Создание сети и настройка основных параметров устройства](#ospf)

2. [Настройка сети OSPFv2 для нескольких областей](#ospf1)

3. [Настройка межобластных суммарных маршрутов](#ospf2)

   

## Создание сети и настройка основных параметров устройства<a name="ospf"></a>

1. Создадим сеть согласно топологии

2. Выполним запуск и перезагрузку маршрутизаторов

3. Произведем базовую настройку маршрутизаторов

   Пример настройки

   ```
   (config)#no ip domain-lookup 
   (config)#hostname R1 
   (config)#interface s1/0
   (config-if)#ip address 192.168.12.1 255.255.255.252
   (config-if)#no shutdown  
   (config-if)#bandwidth 128
   (config-if)#clock rate 128000
   (config-if)#exit
   (config)#interface Lo0
   (config-if)#ip address 209.165.200.225 255.255.255.252 
   (config-if)#exit
   (config)#interface Lo1
   (config-if)#ip address 192.168.1.1 255.255.255.0 
   (config-if)#exit
   (config)#interface Lo2
   (config-if)#ip address 192.168.2.1 255.255.255.0 
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

   

4. Проверить наличие подключения на уровне 3

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/1.4R1.png)

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/1.4R2.png)

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/1.4R3.png)

   

##  Настройка сети OSPFv2 для нескольких областей<a name="ospf1"></a>

1. ##### Определим типы маршрутизаторов OSPF в топологии.

   Магистральными маршрутизаторами являются R1 и R2, потому что они находят в зоне 0

   ASBR будет являться маршрутизатор R1, так как через него идет связь с внешним миром
   
   ABR маршрутизаторами будут являться R1 и R2 так как они находятся на границе нескольких зон
   
   Внутренним маршрутизатором будет являться R3

2. ##### Настроим протокол OSPF на маршрутизаторе R1.

   ```
   R1(config)#router ospf 1
   R1(config-router)#router-id 1.1.1.1 
   
   (config-router)#do sh ip route
   Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
          D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
          N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
          E1 - OSPF external type 1, E2 - OSPF external type 2
          i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
          ia - IS-IS inter area, * - candidate default, U - per-user static route
          o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
          a - application route
   
      + - replicated route, % - next hop override
   
   Gateway of last resort is not set
   
         192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
   
   C        192.168.1.0/24 is directly connected, Loopback1
   L        192.168.1.1/32 is directly connected, Loopback1
         192.168.2.0/24 is variably subnetted, 2 subnets, 2 masks
   C        192.168.2.0/24 is directly connected, Loopback2
   L        192.168.2.1/32 is directly connected, Loopback2
         192.168.12.0/24 is variably subnetted, 2 subnets, 2 masks
   C        192.168.12.0/30 is directly connected, Serial1/0
   L        192.168.12.1/32 is directly connected, Serial1/0
         209.165.200.0/24 is variably subnetted, 2 subnets, 2 masks
   C        209.165.200.224/30 is directly connected, Loopback0
   L        209.165.200.225/32 is directly connected, Loopback0
   
   R1(config)#router ospf 1
   R1(config-router)#network 192.168.1.0 0.0.0.255 area 1
   R1(config-router)#network 192.168.2.0 0.0.0.255 area 1
   R1(config-router)#network 192.168.12.0 0.0.0.3 area 0
   R1(config-router)#passive-interface Lo1
   R1(config-router)#passive-interface Lo2
   R1(config-router)#exit
   
   R1(config)#ip route 0.0.0.0 0.0.0.0 Lo0
                   
   
   R1(config)#router ospf 1
   R1(config-router)#default-information originate
   ```

   

3. ##### Настроим протокол OSPF на маршрутизаторе R2.

   ```
   R2(config-router)#router-id 2.2.2.2
   R2(config-router)#do sh ip route
   Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
          D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
          N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
          E1 - OSPF external type 1, E2 - OSPF external type 2
          i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
          ia - IS-IS inter area, * - candidate default, U - per-user static route
          o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
          a - application route
          + - replicated route, % - next hop override
   
   Gateway of last resort is not set
   
         192.168.6.0/24 is variably subnetted, 2 subnets, 2 masks
   
   C        192.168.6.0/24 is directly connected, Loopback6
   L        192.168.6.1/32 is directly connected, Loopback6
         192.168.12.0/24 is variably subnetted, 2 subnets, 2 masks
   C        192.168.12.0/30 is directly connected, Serial1/0
   L        192.168.12.2/32 is directly connected, Serial1/0
         192.168.23.0/24 is variably subnetted, 2 subnets, 2 masks
   C        192.168.23.0/30 is directly connected, Serial1/1
   L        192.168.23.1/32 is directly connected, Serial1/1
   
   R2(config-router)#network 192.168.6.0 0.0.0.255 area 3
   R2(config-router)#network 192.168.12.0 0.0.0.3 area 0
   R2(config-router)#
   *May  1 10:39:27.415: %OSPF-5-ADJCHG: Process 1, Nbr 1.1.1.1 on Serial1/0 from LOADING to FULL, Loading Done
   R2(config-router)#network 192.168.23.0 0.0.0.3 area 3
   R2(config-router)#passive-interface Lo6
   ```

   

4. ##### Настроим протокол OSPF на маршрутизаторе R3.

   ```
   R3(config)#router ospf 1
   R3(config-router)#router-id 
   R3(config-router)#router-id 3.3.3.3
   R3(config-router)#do sh ip rout conn
   Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
          D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
          N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
          E1 - OSPF external type 1, E2 - OSPF external type 2
          i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
          ia - IS-IS inter area, * - candidate default, U - per-user static route
          o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
          a - application route
          + - replicated route, % - next hop override
   
   Gateway of last resort is not set
   
   192.168.4.0/24 is variably subnetted, 2 subnets, 2 masks
   
   C        192.168.4.0/24 is directly connected, Loopback4
   L        192.168.4.1/32 is directly connected, Loopback4
         192.168.5.0/24 is variably subnetted, 2 subnets, 2 masks
   C        192.168.5.0/24 is directly connected, Loopback5
   L        192.168.5.1/32 is directly connected, Loopback5
         192.168.23.0/24 is variably subnetted, 2 subnets, 2 masks
   C        192.168.23.0/30 is directly connected, Serial1/1
   L        192.168.23.2/32 is directly connected, Serial1/1
   R3(config-router)#network 192.168.4.0 0.0.0.255 area 3
   R3(config-router)#network 192.168.5.0 0.0.0.255 area 3
   R3(config-router)#network 192.168.23.0 0.0.0.3 area 3
   R3(config-router)#
   *May  1 10:52:12.615: %OSPF-5-ADJCHG: Process 1, Nbr 2.2.2.2 on Serial1/1 from LOADING to FULL, Loading Done
   R3(config-router)#pass
   R3(config-router)#passive-interface Lo4
   R3(config-router)#passive-interface Lo5
   ```

   

5. ##### Убедимся в правильности настройки протокола OSPF и в установлении отношений смежности между маршрутизаторами.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.5R1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.5R2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.5R3.png)

R1 является ABR и ASBR 

R2 является ABR 

R3 является внутренним роутером

   Введем команду **show ip ospf neighbor**, чтобы убедиться в установлении отношений смежности OSPF между маршрутизаторами.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.5bR1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.5bR2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.5bR3.png)

6. ##### Настроим аутентификацию MD5 для всех последовательных интерфейсов.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.7R1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.7R2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.7R3.png)

7. ##### Проверим восстановление отношение смежности OSPF

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.8R1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.8R2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/2.8R3.png)



##  Настройка межобластных суммарных маршрутов<a name="ospf2"></a>

1. ##### Просмотрим таблицы маршрутизации OSPF для всех маршрутизаторов

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.1aR1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.1aR2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.1aR3.png)

2. ##### Просмотрим базы данных LSDB на всех маршрутизаторах.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.2aR1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.2aR2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.2aR3.png)

3. ##### Настроим межобластные суммарные маршруты.

На R1

```
R1(config)# router ospf 1
R1(config-router)# area 1 range 192.168.0.0 255.255.252.0
```

На R2

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/Расчет%20зоны%203.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.3dR2.png)

4. ##### Повторно отобразим таблицы маршрутизации OSPF для всех маршрутизаторов

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.4R1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.4R2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.4R3.png)

5. ##### Посмотрим базы данных LSDB на всех маршрутизаторах

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.5R1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.5R2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.5R3.png)

6. ##### Проверим наличие сквозного соединения.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.7R1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.7R2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab07/Sreenshots/3.7R3.png)

Заключение

Разделение на зоны снижает нагрузки на роутеры. Один регион просчитывает маршруты и отдает в другие регионы через ABR роутеры. Всего должен быть регион 0 для защиты от петель. 
