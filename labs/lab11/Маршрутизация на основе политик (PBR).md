# Маршрутизация на основе политик (PBR)

Топология

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab11/Топология%20для%20PBR.png)

**Цель**: Настроить политику маршрутизации в офисе Чокурдах Распределить трафик между 2 линками

##### **Задания**: 

1. Настроить политику маршрутизации для сетей офиса Чокурдах
2. Распределить трафик между двумя линками с провайдером в Чокурдах
3. Настроить отслеживание линка через технологию IP SLA в Чокурдах
4. Настроить для офиса Лабытнанги маршрут по-умолчанию

## Настроить политику маршрутизации для сетей офиса Чокурдах и Распределить трафик между двумя линками с провайдером в Чокурдах

R28

```
ip access-list standard VLAN_50 
	permit 192.168.50.0 0.0.0.255
	deny any 
	exit
ip access-list standard VLAN_60 
	permit 192.168.60.0 0.0.0.255
	deny any 
	exit
ipv6 access-list VLAN_50v6
	permit ipv6 any 1aa:bacc:2020:f1::/64
	exit
ipv6 access-list VLAN_60v6
	permit ipv6 any 1aa:bacc:2020:f2::/64
	exit
Route-map CHOKURDAH permit 10
	match ip address VLAN_50
	match ipv6 address VLAN_50v6 
	set ip next-hop 109.126.20.238
	set ipv6 next-hop 11AA:BACC:1000:1::26 
	exit
Route-map CHOKURDAH permit 20
	match ip address VLAN_60
	match ipv6 address VLAN_60v6 
	set ip next-hop 109.126.19.238
	set ipv6 next-hop 11AA:BACC:1000:A2::25
	exit
int e0/2 
	ip policy route-map CHOKURDAH
	ip access-group VLAN_50 in
	ip access-group VLAN_60 in
	ipv6 traffic-filter VLAN_50v6 in 
	ipv6 traffic-filter VLAN_60v6 in
```

## Настроить отслеживание линка через технологию IP SLA в Чокурдах

```
ip sla 1
 icmp-echo 109.126.20.237 source-interface e0/0
 	timeout 5000
 	frequency 5
ip sla schedule 1 life forever start-time now

ip sla 2
 icmp-echo 109.126.19.237 source-interface e0/1
 timeout 5000
 frequency 5
ip sla schedule 2 life forever start-time now

ip sla 3
 icmp-echo 11AA:BACC:1000:C1::28 source-interface e0/0 
 timeout 5000
 frequency 5
 ip sla schedule 3 life forever start-time now

ip sla 4
 icmp-echo 11AA:BACC:1000:A2::28 source-interface e0/1
 timeout 5000
 frequency 5
 ip sla schedule 4 life forever start-time now

track 1 ip sla 1 reachability
 delay down 10 up 5

track 2 ip sla 2 reachability
 delay down 10 up 5

track 3 ip sla 3 reachability
 delay down 10 up 5

track 4 ip sla 4 reachability
 delay down 10 up 5
 
ip access-list standard VLAN_60 
	permit 192.168.60.3 0.0.0.255
	deny any

ip access-list standard VLAN_50
	permit 192.168.60.3 0.0.0.255
	deny any

route-map CHOKURDAH permit 10
	match ip address VLAN_50 
	set ip next-hop verify-availability 109.126.20.238 2 track 2
	set ip next-hop verify-availability 11AA:BACC:1000:С1::26 3 track 3 (В eve-ng эта команда не поддерживается)
	exit
route-map CHOKURDAH permit 20
	match ip address VLAN_60
	set ip next-hop verify-availability 109.126.19.238 1 track 1
	set ip next-hop verify-availability 11AA:BACC:1000:A2::28 4 track 4 (В eve-ng эта команда не поддерживается)
	exit

```

## Настроить для офиса Лабытнанги маршрут по-умолчанию

R25

```
R25(config)#                                                                             R25(config)#route-map STATIC permit 10                                                   R25(config-route-map)#                                                                   R25(config-route-map)#match int e0/3                                                     R25(config-route-map)#                                                                   R25(config-route-map)#set ip default next-hop 109.126.18.237
```

```
conf t 
	ip route 0.0.0.0 0.0.0.0 109.126.18.237
```

