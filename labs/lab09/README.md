# Лабораторная работа. Настройка расширенных функций EIGRP для IPv4

Топология

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/Топология.png)

Таблица адресации

| Устройство | Интерфейс | IP-адрес      | Маска подсети   | Шлюз по умолчанию |
| ---------- | --------- | ------------- | --------------- | ----------------- |
| R1         | e0/0      | 192.168.1.1   | 255.255.255.0   | -                 |
|            | s1/0      | 192.168.12.1  | 255.255.255.252 | -                 |
|            | s1/2      | 192.168.13.1  | 255.255.255.252 | -                 |
|            | Lo1       | 192.168.11.1  | 255.255.255.252 | -                 |
|            | Lo5       | 192.168.11.5  | 255.255.255.252 | -                 |
|            | Lo9       | 192.168.11.9  | 255.255.255.252 | -                 |
|            | Lo13      | 192.168.11.13 | 255.255.255.252 | -                 |
| R2         | e0/0      | 192.168.2.1   | 255.255.255.0   | -                 |
|            | s1/0      | 192.168.12.2  | 255.255.255.252 | -                 |
|            | s1/1      | 192.168.23.1  | 255.255.255.252 | -                 |
|            | Lo1       | 192.168.22.1  | 255.255.255.252 | -                 |
| R3         | e0/0      | 192.168.3.1   | 255.255.255.0   | -                 |
|            | s1/2      | 192.168.13.2  | 255.255.255.252 | -                 |
|            | s1/1      | 192.168.23.2  | 255.255.255.252 | -                 |
|            | Lo1       | 192.168.33.1  | 255.255.255.252 | -                 |
|            | Lo5       | 192.168.33.5  | 255.255.255.252 | -                 |
|            | Lo9       | 192.168.33.9  | 255.255.255.252 | -                 |
|            | Lo13      | 192.168.33.13 | 255.255.255.252 | -                 |
| PC-A       | NIC       | 192.168.1.3   | 255.255.255.0   | 192.168.1.1       |
| PC-B       | NIC       | 192.168.2.3   | 255.255.255.0   | 192.168.2.1       |
| PC-C       | NIC       | 192.168.3.3   | 255.255.255.0   | 192.168.3.1       |

Задачи

1. [Создание сети и настройка основных параметров устройства](#A1)

2. [Настройка EIGRP и проверка подключения](#A2)

3. [Настройка EIGRP для автоматического объединения](#A3)

4. [Настройка и распространение статического маршрута по умолчанию](#A4)

5. [Выполнение точной настройки EIGRP](#A5)

Настроим параметры использования пропускной способности для EIGRP.

Настроим интервал отправки пакетов приветствия (hello) и таймер удержания для EIGRP

## Создание сети и настройка основных параметров устройства<a name="A1"></a>

1. Создадим сеть согласно топологии.

2. Настроим узлы ПК.

3. Выполним запуск и перезагрузку маршрутизаторов.

4. Произведём базовую настройку маршрутизаторов.

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

## Настройка EIGRP и проверка подключения<a name="A2"></a>

1. Настроим EIGRP

   На R1 настроим EIGRP AS 1 для всех сетей с прямым подключением 

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/2.1a.png)

2. Для интерфейса локальной сети маршрутизатора R1 отключим передачу пакетов приветствия.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/2.1b.png)

3. Настроим пропускную способность 1024 Kb/s на интерфейсе s1/0, а для интерфейса s1/2 64 Kb/s

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/2.1c.png)

4. Настроим EIGRP на R2

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/2.1d.png)

5. Настроим EIGRP на R3

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/2.1e.png)

6. Проверим связь

Все компьютеры должны успешно отправлять эхо-запросы друг другу

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/2.2pc-1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/2.2pc-2.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/2.2pc-3.png)

## Настройка EIGRP для автоматического объединения<a name="A3"></a>

В части 3 добавим интерфейсы loopback и включиv автоматическое объединение EIGRP на маршрутизаторах R1 и R3. Вы также пронаблюдаете за изменениями в таблице маршрутизации R2

1. ##### Настроим EIGRP для автоматического объединения.

Введем команду **show ip protocols**

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/3.1a.png)

Автоматическое объединение по умолчанию выключено.

Настроим loopback-адреса на R1.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/3.1c.png)

Выполним команду **show ip route eigrp** на R2

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/3.1d.png)

Видно что до каждого loopback адреса отдельная запись в таблице

На маршрутизаторе R1 выполним команду **auto-summary** в рамках процесса EIGRP

```
R1(config)# router eigrp 1
R1(config-router)# auto-summary
```

Посмотрим как изменилась таблица маршрутизации на R2

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/3.1eR2.png)

Теперь в таблице 1 запись 192.168.11.0

Теперь выполним повторно весь процесс настройки loopback адресов и сразу автоматическое суммирование для R3. Взглянем на таблицу маршрутизации R2.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/3.1f.png)

Вместо записи о каждом адресе loopback одна запись 192.168.33.0

## Настройка и распространение статического маршрута по умолчанию<a name="A4"></a>

В части 4 нам необходимо настроить статический маршрут по умолчанию на R2 и распространить его на все остальные маршрутизаторы

1. Настроим loopback-адрес на R2

2. Настроим статический маршрут по умолчанию с выходным интерфейсом Lo1

```
R2(config)# ip route 0.0.0.0 0.0.0.0 Lo1
```

3. Выполним команду **redistribute static** в рамках процесса EIGRP, чтобы распространить статический маршрут по умолчанию на другие участвующие маршрутизаторы.

```
R2(config)# router eigrp 1
R2(config-router)# redistribute static
```

4. Используем команду **show ip protocols** на маршрутизаторе R2, чтобы проверить, распространился ли этот статический маршрут.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/4d.png)

5. На маршрутизаторе R1 выполним команду **show ip route eigrp** **| include 0.0.0.0**, чтобы просмотреть инструкции, относящиеся к маршруту по умолчанию

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/4e.png)

Видно что статический маршрут по умолчанию представлен в виде внешнего EIGRP. Административная дистанция равна у него 170.

## Подгонка EIGRP<a name="A5"></a>

В части 5 нам предстоит настроить процент пропускной способности, который может быть использован интерфейсом для трафика EIGRP, а также изменить интервал приветствия и таймеры удержания для интерфейсов EIGRP.

1. #####  Настроим параметры использования пропускной способности для EIGRP.

a.   Настроим последовательный канал между маршрутизаторами R1 и R2, чтобы разрешить трафику EIGRP использовать только 75 % пропускной способности канала.

```
R1(config)# interface s0/0/0
R1(config-if)# ip bandwidth-percent eigrp 1 75
R2(config)# interface s0/0/0
R2(config-if)# ip bandwidth-percent eigrp 1 75
```

b.   Настроим последовательный канал между маршрутизаторами R1 и R3, чтобы разрешить трафику EIGRP использовать только 40 % пропускной способности канала.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/5.1bR1.png)

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/5.1bR3.png)

2. ##### Настроим интервал отправки пакетов приветствия (hello) и таймер удержания для EIGRP.

a.   На маршрутизаторе R2 используем команду **show ip eigrp interfaces** **detail** для просмотра интервала приветствия и таймера задержки для EIGRP.

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab09/Screenshot/5.2a.png)

Hello-interval по умолчанию 5с
Hold-time по умолчанию 15с

b.   Для интерфейсов s1/0 и s1/2 маршрутизатора R1 настроим интервал приветствия равным 60 секунд, а таймер удержания равным 180 секунд, именно в этом порядке.

```
R1(config)# interface s1/0
R1(config-if)# ip hello-interval eigrp 1 60
R1(config-if)# ip hold-time eigrp 1 180
R1(config)# interface s1/2
R1(config-if)# ip hello-interval eigrp 1 60
R1(config-if)# ip hold-time eigrp 1 180
```

c.   Для последовательных интерфейсах маршрутизаторов R2 и R3 настроим интервал приветствия равным 60 секунд, а таймер удержания равным 180 секунд.

```
R2(config)# int s1/0
R2(config-if)# ip hello-interval eigrp 1 60
R2(config-if)# ip hold-time eigrp 1 180
R2(config-if)# int s1/1
R2(config-if)# ip hello-interval eigrp 1 60
R2(config-if)# ip hold-time eigrp 1 180
```

```
R3(config)# int s1/1
R3(config-if)# ip hello-interval eigrp 1 60
R3(config-if)# ip hold-time eigrp 1 180
R3(config-if)# int s1/2
R3(config-if)# ip hello-interval eigrp 1 60
R3(config-if)# ip hold-time eigrp 1 180
```

##### Заключение

Преимуществом объединения маршрутов является уменьшение записей в таблице маршрутизации и за счет этого быстрая скорость работы сети.

Протокол EIGRP использует таймер удержания (hold-интервал), чтобы определить максимальное время ожидания роутером получения следующего пакета приветствия, прежде чем соседний маршрутизатор будет считаться недоступным. По умолчанию hold-таймер втрое больше hello-интервала. В большинстве сетей стандартными значениями для таймера приветствия и удержания считаются 5 и 15 секунд соответственно. По истечении времени удержания EIGRP объявляет маршрут неактивным и алгоритм DUAL выполняет поиск нового пути, отправляя соответствующие запросы.
