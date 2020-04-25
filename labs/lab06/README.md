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

1. ##### Создадим сеть согласно топологии

2. ##### Выполним запуск и перезапуск маршрутизаторов

3. ##### Произведем базовую настройку маршрутизаторов

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

4. ##### Настроим узлы ПК

5. ##### Проверим связь

   R1

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/1.5R1.png)

   

   R2

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/1.5R2.png)

   

   R3

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/1.5R3.png)



## Настройка и проверка маршрутизации OSPF<a name="OSPF2"></a>

1. ##### Настройка протокола OSPF на маршрутизаторе R1

   ```
   R1(config)# router ospf 1
   R1(config-router)# network 192.168.1.0 0.0.0.255 area 0
   R1(config-router)# network 192.168.12.0 0.0.0.3 area 0
   R1(config-router)# network 192.168.13.0 0.0.0.3 area 0
   ```

2. ##### Настройка OSPF на маршрутизаторе R2 и R3

   R2

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/2.2R2.png)

   

   R3

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/2.2R3.png)

3. ##### Проверим информацию о соседних устройствах и маршрутизацию OSPF

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/2.3a.png)

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/2.3b.png)

   Для удобства можно сразу писать **show ip route ospf**

   4. ##### Проверим параметры протокола OSPF 

      ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/2.4.png)

   5. ##### Проверим данные процесса OSPF

      ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/2.5.png)

6. ##### Проверим параметры интерфейса OSPF

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/2.6.png)

7. ##### Проверим наличие сквозного соединения 

   PC-A

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/2.7PC1.png)

   

   PC-B

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/2.7PC2.png)

   

PC-C



![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/2.7PC3.png)

## Изменение назначенных идентификаторов маршрутизаторов<a name="OSPF3"></a>

1. ##### Изменим идентификатор маршрутизаторов с помощью loopback-адресов

   ```
   R1(config)# interface lo0
   R1(config-if)# ip address 1.1.1.1 255.255.255.255
   R1(config-if)# end
   ```

   R2

   ```
   R2(config)# interface lo0
   R2(config-if)# ip address 2.2.2.2 255.255.255.255
   R2(config-if)# end
   ```

   R3

   ```
   R3config)# interface lo0
   R3config-if)# ip address 3.3.3.3 255.255.255.255
   R3config-if)# end
   ```

   Сохраним текущую конфигурацию в загрузочную конфигурацию на всех трех маршрутизаторах

   Перезагрузим маршрутизаторы командой **reload**

   Введем команду **show ip protocols** чтобы посмотреть новый идентификатор маршрутизаторов

   R1

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/3.1eR1.png)

   

   R2

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/3.1eR2.png)

   

   R3

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/3.1eR3.png)

   Введем команду **show ip ospf neighbor** чтобы посмотреть изменения идентификатор соседних маршрутизаторов

   R1

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/3.1fR1.png)

   

   R2

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/3.1fR2.png)

   

   R3

   

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/3.1fR3.png)

   2. ##### Изменим идентификатор маршрутизатора R1 с помощью команды `router-id`

      ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/3.2a.png)

      Для R2 и R3 настроим идентификаторы 22.22.22.22 и 33.33.33.33. Затем используем команду **clear ip ospf process** для сброса процесса маршрутизации ospf

      

   Введем команду **show ip protocols**, чтобы проверить, изменился ли идентификатор на маршрутизаторе R1

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/3.2d.png)



​    Выполним команду **show ip ospf neighbor** на маршрутизаторе R1, чтобы убедиться, что новые идентификаторы для маршрутизаторов R2 и R3 содержатся в списке.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/3.2e.png)

## Настройка пассивных интерфейсов OSPF<a name="OSPF4"></a>

1. ##### Настройка пассивных интерфейсов

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.1a.png)

   Выполним команду **passive-interface**, чтобы интерфейс e0/0 маршрутизатора R1 стал пассивным.

   ```
   R1(config)# router ospf 1
   R1(config-router)# passive-interface е0/0
   ```

   Повторно выполниv команду **show ip ospf interface е0/0**, чтобы убедиться, что интерфейс e0/0 стал пассивным (забыл скрин сделать)

   ```
   R1# show ip ospf interface e0/0
   Ethernet0/0 is up, line protocol is up 
     Internet Address 192.168.1.1/24, Area 0, Attached via Network Statement
     Process ID 1, Router ID 11.11.11.11, Network Type BROADCAST, Cost: 1
     Topology-MTID    Cost    Disabled    Shutdown      Topology Name
           0           1         no          no            Base
     Transmit Delay is 1 sec, State DR, Priority 1
     Designated Router (ID) 11.11.11.11, Interface address 192.168.1.1
     No backup designated router on this network
     Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
       oob-resync timeout 40
       No Hellos (Passive interface) 
     Supports Link-local Signaling (LLS)
     Cisco NSF helper support enabled
     IETF NSF helper support enabled
     Index 1/1, flood queue length 0
     Next 0x0(0)/0x0(0)
     Last flood scan length is 0, maximum is 0
     Last flood scan time is 0 msec, maximum is 0 msec
     Neighbor Count is 0, Adjacent neighbor count is 0 
     Suppress hello for 0 neighbor(s)
   ```

   Введем команду **show ip route** на маршрутизаторах R2 и R3, чтобы убедиться, что маршрут к сети 192.168.1.0/24 остается доступным.

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.1R2.png)

   ![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.1R3.png)

   ##### 2. Настроим на маршрутизаторе пассивный интерфейс в качестве интерфейса по умолчанию.

a.   Выполним команду **show ip ospf neighbor** на маршрутизаторе R1, чтобы убедиться, что R2 указан в качестве соседнего устройства OSPF.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.2a.png)

b.   Введем команду **passive-interface default** на маршрутизаторе R2, чтобы задать настройку по умолчанию всех интерфейсов OSPF в качестве пассивных.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.2b.png)

с.   Повторно введем команду **show ip ospf neighbor** на маршрутизаторе R1. После истечения таймера простоя маршрутизатор R2 больше не будет указан как соседнее устройство OSPF

```
R1# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
33.33.33.33       0   FULL/  -        00:00:34    192.168.13.2    Serial1/2
```

d.  Выполним команду **show ip ospf interface S1/0** на маршрутизаторе R2, чтобы просмотреть состояние OSPF для интерфейса S1/0.

```
R2# show ip ospf interface s1/0
Serial1/0 is up, line protocol is up 
  Internet Address 192.168.12.2/30, Area 0, Attached via Network Statement
  Process ID 1, Router ID 22.22.22.22, Network Type POINT_TO_POINT, Cost: 64
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           64        no          no            Base
  Transmit Delay is 1 sec, State POINT_TO_POINT
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    No Hellos (Passive interface) 
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 2/2, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 0, maximum is 0
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 0, Adjacent neighbor count is 0 
  Suppress hello for 0 neighbor(s)

```

е.   Если все интерфейсы маршрутизатора R2 являются пассивными, то информация маршрутизации не будет объявляться. В этом случае у маршрутизаторов R1 и R3 теперь должен отсутствовать маршрут к сети 192.168.2.0/24. Это можно проверить командой **show ip route**.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.2eR1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.2eR3.png)

f.   На маршрутизаторе R2 выполним команду **no passive-interface**, чтобы маршрутизатор отправлял и получал обновления маршрутизации OSPF. После ввода этой команды появится уведомление о том, что были установлены соседские отношения смежности с маршрутизатором R1.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.2fR2.png)

g.   Повторно выполним команды **show ip route** и **show ip ospf neighbor** на маршрутизаторах R1 и R3 и найдите маршрут к сети 192.168.2.0/24.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.2gR1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.2gR11.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.2gR3.png)

Исходя из видно что R3 для маршрута к сети 192.168.2.0/24 использует интерфейс S1/2. Cсуммарная метрика стоимости для сети 192.168.2.0/24 на R3 равна 138. На R1 как соседнее устройство OSPF отображается R2 и R3, при этом на R3 отображается только R1. Так происходит потому что интерфейс на R2 в сторону R3 S1/1 настроен как пассивный интерфейс

h.   Настроим интерфейс S1/1 маршрутизатора R2 так, чтобы разрешить ему объявлять маршруты OSPF. 

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.2hR2.png)

i.   Повторно введем команду **show ip route** на маршрутизаторе R3.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/4.2iR3.png)

Видно теперь что интерфейс используется s1/1 и суммарная метрика стала 74.



## Изменение метрик OSPF<a name="OSPF5"></a>

1. ##### Изменим заданную пропускную способность для маршрутизаторов.

a.   Выполним команду **show interface** на маршрутизаторе R1, чтобы просмотреть значение пропускной способности по умолчанию для интерфейса e0/0.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.1a.png)

b.   Введите команду **show ip route ospf** на маршрутизаторе R1, чтобы определить маршрут к сети 192.168.3.0/24.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.1b.png)

с.   Выполним команду **show ip ospf interface** на маршрутизаторе R3, чтобы определить стоимость маршрутизации для интерфейса е0/0.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.1c.png)

d.   Выполним команду **show ip ospf interface s1/2** на маршрутизаторе R1, чтобы просмотреть стоимость маршрутизации для интерфейса S1/2.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.1d.png)

Как видно из результатов команды **show ip route**, сумма метрик стоимости этих двух интерфейсов является суммарной стоимостью маршрута к сети 192.168.3.0/24 для маршрутизатора R3, рассчитываемой по формуле 10 + 64 = 74.

e.  Чтобы изменить параметр эталонной пропускной способности по умолчанию, выполним команду **auto-cost reference-bandwidth 10000** на маршрутизаторе R1. С этим параметром стоимость интерфейсов 10 Гбит/с будет равна 1, стоимость интерфейсов 1 Гбит/с будет равна 10, а стоимость интерфейсов 100 Мбит/c будет равна 100

```
R1(config)# router ospf 1
R1(config-router)# auto-cost reference-bandwidth 10000
```

f.   Выполним  команду **auto-cost reference-bandwidth 10000** на маршрутизаторах R2 и R3.

g.   Повторно выполним команду **show ip ospf interface**, чтобы просмотреть новую стоимость интерфейса e0/0 на R3 и интерфейса S1/2 на R1

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.1g.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.1gR1.png)

h.   Повторно введите команду **show ip route ospf** для просмотра новой накопленной стоимости для маршрута 192.168.3.0/24 (1000 + 6476 = 7476).

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.1hR1.png)

i.   Чтобы восстановить для эталонной пропускной способности значение по умолчанию, на всех трех маршрутизаторах выполните команду **auto-cost reference-bandwidth 100**.

```
R1(config)# router ospf 1
R1(config-router)# auto-cost reference-bandwidth 100
```

Почему может понадобиться изменить эталонную пропускную способность OSPF по умолчанию?

Для расчета стоимости. в OSPF метрика представляет собой оценку эффективности связи в канале связи: **чем меньше стоимость метрики, тем эффективнее организация связи**. Стоимость метрики, оценивающая пропускную способность канала вычисляется по формуле 

​                                               **cost=reference bandwidth / link bandwidth**

2. ##### Изменим пропускную способность для интерфейса

a.   Выполним команду **show interface s1/0** на маршрутизаторе R1, чтобы просмотреть текущую пропускную способность на интерфейсе S1/0. Хотя тактовая частота (скорость передачи данных) для этого интерфейса была задана равной 128 Кбит/с, пропускная способность по-прежнему показывается как 1544 Кбит/с.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.2a.png)

b.   Введем команду **show ip route ospf** на маршрутизаторе R1 для просмотра накопленной стоимости маршрута к сети 192.168.23.0/24 с использованием интерфейса S1/0. Обратим внимание, что к сети 192.168.23.0/24 есть два маршрута с равной стоимостью (128): один через интерфейс S1/0, а другой через S1/2.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.2b.png)

с.    Выполним команду **bandwidth 128**, чтобы установить для интерфейса S1/0 пропускную способность равной 128 Кбит/c

```
R1(config)# interface s1/0
R1(config-if)# bandwidth 128
```

d.   Повторно введем команду **show ip route ospf**. В таблице маршрутизации больше не показывается маршрут к сети 192.168.23.0/24 через интерфейс S1/0. Это связано с тем, что оптимальный маршрут с наименьшей стоимостью проложен через S1/2.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.2d.png)

е.   Введем команду **show ip ospf interface brief**. Стоимость для интерфейса S1/0 изменилась с 64 на 781, что является точным представлением стоимости скорости канала.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.2e.png)

f.   Изменим на маршрутизаторе R1 пропускную способность для интерфейса S1/2 на значение, равное значению для интерфейса S1/0.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.2f.png)

g.   Повторно введем команду **show ip route ospf** для просмотра накопленной стоимости обоих маршрутов к сети 192.168.23.0/24. Обратим внимание, что к сети 192.168.23.0/24 есть два маршрута с равной стоимостью (845): один через интерфейс 1/0, а другой через S1/2.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.2g.png)

Стоимость 845 происходит от сложения стоимости s1/0 781+s1/1 64. А стоимость 791 происходит от 10 + 781.

3. ##### Изменим стоимость маршрута

a.   Введем команду **show ip route ospf** на маршрутизаторе R1.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.3a.png)

b.   Выполним команду **ip ospf cost 1565** для интерфейса S1/2 маршрутизатора R1. Стоимость 1565 оказывается выше суммарной стоимости маршрута, проходящего через маршрутизатор R2 (1562).

```
R1(config)# interface s0/0/1
R1(config-if)# ip ospf cost 1565
```

с.   Повторно введем команду **show ip route ospf** на маршрутизаторе R1, чтобы отобразить изменения, внесенные в таблицу маршрутизации. Теперь все маршруты OSPF для маршрутизатора R1 проходят через маршрутизатор R2.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab06/Screeshots/5.3c.png)

Заключение
OSPF относится к классу протоколов маршрутизации **Link State**. 
Если в сети в качестве узлов используются другие маршрутизаторы то очень важно, чтобы все они имели уникальные имена. Это необходимо для того, чтобы глядя на полученные из разных источников LSA, понять, что речь идёт об одних и тех же устройствах. В качестве такого уникального имени в OSPF используется поле «Router ID». Идентификатор маршрутизатора выглядит как ip адрес, то есть, состоит из четырёх октетов, разделённых точками.
В этой работе не рассматривался процесс выбора DR/BDR так как все маршрутизаторы были соединены друг с другом и находились в одной зоне. Когда имеется сеть со множественным доступом и в ней более двух маршрутизаторов, то один выбирается на роль DR, а второй – на BDR. Каждый маршрутизатор, который хочет отправить, например, LSA, шлёт его не всем в сети, а на специальный мультикастовый адрес, который слушает только DR и BDR, а DR рассылает LSA всем в сети. Таким образом у нас получается не связь «каждый с каждым», а связь «каждый с DR» + «DR с каждым», что позволяет существенно снизить нагрузку в случае, если маршрутизаторов в таком сегменте много.
Пассивными мы делаем интерфесы для безопасности, чтобы например компьютер не смог зафлудить нашу сеть
