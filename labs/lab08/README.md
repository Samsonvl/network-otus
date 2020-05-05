# Лабораторная работа. Базовая настройка протокола EIGRP для IPv4

Топология

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/Топология.png)



Таблица адресации

| Устройства | Интерфейс | IP-маска    | Маска подсети   | Шлюз по умолчанию |
| ---------- | --------- | ----------- | --------------- | ----------------- |
| R1         | s1/0      | 10.1.1.1    | 255.255.255.252 | -                 |
|            | s1/2      | 10.3.3.1    | 255.255.255.252 | -                 |
|            | e0/0      | 192.168.1.1 | 255.255.255.0   | -                 |
| R2         | s1/0      | 10.1.1.2    | 255.255.255.252 | -                 |
|            | s1/1      | 10.2.2.2    | 255.255.255.252 | -                 |
|            | e0/0      | 192.168.2.1 | 255.255.255.0   | -                 |
| R3         | s1/1      | 10.2.2.1    | 255.255.255.252 | -                 |
|            | s1/2      | 10.3.3.2    | 255.255.255.252 | -                 |
|            | e0/0      | 192.168.3.1 | 255.255.255.0   | -                 |
| PC-A       | NIC       | 192.168.1.3 | 255.255.255.0   | 192.168.1.1       |
| PC-B       | NIC       | 192.168.2.3 | 255.255.255.0   | 192.168.2.1       |
| PC-C       | NIC       | 192.168.3.3 | 255.255.255.0   | 192.168.3.1       |

Задачи

1. [Построение сети и проверка соединения]()

2. [Настройка маршрутизации EIGRP]()

3. [Проверка маршрутизации EIGRP]()

4. [Настройка пропускной способности и пассивных интерфейсов]()

#### Построение сети и проверка связи

1. Создадим сеть согласно топологии.

2. Настроим узлы ПК.

3. Выполним запуск и перезагрузку маршрутизаторов.

4. Произведите базовую настройку маршрутизаторов.

Пример настройки

```
(config)#no ip domain-lookup 
(config)#hostname R1 
(config)#interface s1/0
(config-if)#ip address 10.1.1.1 255.255.255.252
(config-if)#no shutdown  
(config-if)#exit
(config)#interface s1/2
(config-if)#ip address 10.3.3.1 255.255.255.252
(config-if)#no shutdown  
(config-if)#exit
(config)#interface e0/0
(config-if)#ip address 192.168.1.1 255.255.255.0
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

5. Проверим соединение

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/ping%20R1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/ping%20R2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/ping%20R3.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/ping%20pc-a.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/ping%20pc-b.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/ping%20pc-c.png)

## Настройка маршрутизации EIGRP

1. ##### Включим маршрутизацию EIGRP на маршрутизаторе R1. Используем номер автономной системы 10.

```
R1(config)# router eigrp 10
```

2. #####  Объявим напрямую подключенные сети на маршрутизаторе R1, используя шаблонную маску.

```
R1(config-router)# network 10.1.1.0 0.0.0.3
R1(config-router)# network 192.168.1.0 0.0.0.255
R1(config-router)# network 10.3.3.0 0.0.0.3
```

Маска, указывает с помощью нулей какая часть из указанной сети должна совпадать, а с помощью 1 какая часть сети может быть произвольной. На R1 можно было бы не включать для 192.168.1.0, потому что для них по умолчанию используется классовая маска /24

3. ##### Включим маршрутизацию EIGRP и объявим напрямую подключенные сети на маршрутизаторах R2 и R3.

   R2

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/2.3R2.png)

R3

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/2.3R3.png)

4. ##### Проверим наличие сквозного соединения

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/2.4R1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/2.4R2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/2.4R3.png)

## Проверка маршрутизации EIGRP

1. ##### Анализ таблицы соседних устройств EIGRP

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/3.1R1.png)

2. ##### Проанализируем таблицу IP-маршрутизации EIGRP

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/3.2R1.png)

У маршрутизатора R1 два пути к сети 10.2.2.0/30, через R2 и через R3

3. ##### Проанализируем таблицу соседних устройств EIGRP.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/3.3R1.png)

Маршруты находится в состоянии passive, то-есть маршрутизатор не выполняет пересчет маршрута

4. ##### Проверим параметры маршрутизации EIGRP и объявленные сети.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/3.4R1.png)

Автономная сеть под номером  10

Объявляемые сети  
10.1.1.0/30

10.3.3.0/30

192.168.1.0

administrative distance:  internal 90 external 170

## Настройка пропускной способности и пассивных интерфейсов

1. ##### Изучим текущие настройки маршрутизации

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/4.1R1.png)

Пропускная способность 1544Kb/s

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/4.1bR1.png)

Маршрутов к сети 10.2.2.0/30 - 2

2. ##### Изменим пропускную способность на маршрутизаторах.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/4.2aR1.png)

  Изменим пропускную способность для последовательных интерфейсов маршрутизаторов R2 и R3.

```
R2(config)# interface s0/0/0
R2(config-if)# bandwidth 2000
R2(config-if)# interface s0/0/1
R2(config-if)# bandwidth 2000

R3(config)# interface s0/0/0
R3(config-if)# bandwidth 64
R3(config-if)# interface s0/0/1
R3(config-if)# bandwidth 2000
```

3. ##### Проверим изменения пропускной способности.


![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/4.3R1.png)

4. ##### Настроим на маршрутизаторах R1, R2 и R3 интерфейс e0/0 как пассивный

```
R1(config)# router eigrp 10
R1(config-router)# passive-interface e0/0
```



```
R2(config)# router eigrp 10
R2(config-router)# passive-interface e0/0
```



```
R3(config)# router eigrp 10
R3(config-router)# passive-interface e0/0
```

5. ##### Проверим конфигурацию пассивных интерфейсов

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab08/Screenshots/4.5R1.png)

EIGRP относится к дистанционно-векторным протоколам. Преимущество его в быстрой сходимости. Так же в гибкой настройке.
