# Маршрутизация на основе политик (PBR)

**Цель**: Настроить политику маршрутизации в офисе Чокурдах Распределить трафик между 2 линками

##### **Задания**: 

1. Настроить политику маршрутизации для сетей офиса Чокурдах
2. Распределить трафик между двумя линками с провайдером в Чокурдах
3. Настроить отслеживание линка через технологию IP SLA в Чокурдах
4. Настроить для офиса Лабытнанги маршрут по-умолчанию

## Настроить политику маршрутизации для сетей офиса Чокурдах и Распределить трафик между двумя линками с провайдером в Чокурдах

R28

```
R28(config)#ip access-list standard VLAN_50 
R28(config-std-nacl)#permit 192.168.50.0 0.0.0.255
R28(config-std-nacl)#deny any 
R28(config-std-nacl)#exit
R28(config)#ip access-list standard VLAN_60 
R28(config-std-nacl)#permit 192.168.60.0 0.0.0.255
R28(config-std-nacl)#deny any 
R28(config-std-nacl)#exit
R28(config)#Route-map CHOKURDAH permit 10
R28(config-route-map)#match ip address VLAN_50
R28(config-route-map)#set ip next-hop 109.126.20.238
R28(config-route-map)#exit
R28(config)#Route-map CHOKURDAH permit 20
R28(config-route-map)#match ip address VLAN_60
R28(config-route-map)#set ip next-hop 109.126.19.238
R28(config-route-map)#exit
R28(config)#int e0/2 
R28(config-if)#ip policy route-map CHOKURDAH
R28(config-if)#ip access-group VLAN_50 in
R28(config-if)#ip access-group VLAN_60 in
```

## Настроить отслеживание линка через технологию IP SLA в Чокурдах

```
ip sla 1
 icmp-echo 109.126.20.237 source-interface e0/0
 timeout 500
 frequency 3
ip sla schedule 1 life forever start-time now

ip sla 2
 icmp-echo 109.126.19.237 source-interface e0/1
 timeout 500
 frequency 3
ip sla schedule 2 life forever start-time now

track 1 ip sla 1 reachability
 delay down 10 up 5

track 2 ip sla 2 reachability
 delay down 10 up 5
 
ip access-list standard VLAN_60 
	permit 192.168.60.3 0.0.0.255
	deny any

ip access-list standard VLAN_50
	permit 192.168.60.3 0.0.0.255
	deny any

route-map CHOKURDAH permit 10
	match ip address VLAN_50 
	set ip next-hop verify-availability 109.126.20.238 track 2
	exit
route-map CHOKURDAH permit 20
	match ip address VLAN_60
	set ip next-hop verify-availability 109.126.19.238 track 1
	exit

interface FastEthernet0/0
 ip policy route-map POLICY
```

## Настроить для офиса Лабытнанги маршрут по-умолчанию

R25

```
R25(config)#
R25(config)#route-map STATIC permit 10  
R25(config-route-map)#match int e0/3
R25(config-route-map)#set ip default next-hop 109.126.18.237
```

