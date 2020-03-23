# Настройка расширенных сетей VLAN, VTP и DTP
![](Image1.png)

## 	Таблица адресации.



| Заголовок таблицы    | Интерфейс      | IP-адрес                 | Маска подсети       | 
|---------------------:|:---------------|-------------------------:|:--------------------|
| S1                   | VLAN 99        | 192.168.99.1             | 255.255.255.0       | 
| S2                   | VLAN 99        | 192.168.99.2             | 255.255.255.0       | 
| S3                   | VLAN 99        | 192.168.99.3             | 255.255.255.0       | 
| PC-A                 | NIC            | 192.168.10.1             | 255.255.255.0       | 
| PC-B                 | NIC            | 192.168.20.1             | 255.255.255.0       | 
| PC-C                 | NIC            | 192.168.10.2             | 255.255.255.0       | 

## Задание 
1. Настроить VTP
2. Настроить DTP
3. Добавить сети VLAN и назначение портов
4. Настроить расширенные сети VLAN

## Решение 
1. [Настроим VTP](#VTP)
   * [Настройте S2 в качестве сервера VTP](#VTP2) 
   * [Настроим S1 и S3 в качестве клиентов VTP](#VTP1)
2. [Настроим динамический протокол транкинг (DTP)](#DTP)
   * [Настроим динамический магистральный канал между S1 и S2](#DTP1)
   * [Настроим статическоий магистральный канал между S1 и S3](#DTP2)
3. [Добавим сеть VLAN и назначение портов](#VLAN)
   * [Добавим сети VlAN на коммутарах](#VLAN1)
   * [Проверим обновление VTP на коммутаторах S1 и S3](#VLAN2)
   * [Назначим порты сетям VLAN](#VLAN3)
   * [Настроим IP-адреса на коммутаторах](#VLAN4) 
   * [Проверим наличие сквозного соединения](#VLAN5)
4. [Настроим сети VLAN расширенного диапозона](#VLAN6)
   * [Переведем VTP на коммутаторе S1 в прозрачный режим](#VLAN7)
   * [Настроим сеть VLAN расширенного диапазона на коммутаторе S1](#VLAN8)


## Настройка VTP <a name="VTP"></a>
### Настройте S2 в качестве сервера VTP <a name="VTP2"></a>
* Switch(config)#**vtp domain CCNA**
* Domain name already set to CCNA.
* Switch(config)#**vtp mode server**
* Device mode already VTP SERVER.
* Switch(config)#**vtp password cisco**
* Password already set to cisco
* Switch#**sh vtp status**
* VTP Version                     : 2
* Configuration Revision          : 8
* Maximum VLANs supported locally : 250
* Number of existing VLANs        : 9
* VTP Operating Mode              : Server
* VTP Domain Name                 : CCNA
* VTP Pruning Mode                : Disabled
* VTP V2 Mode                     : Disabled
* VTP Traps Generation            : Disabled
* MD5 digest                      : 0x16 0x68 0xF2 0x0B 0x67 0x31 0x81 0x1B 
* Configuration last modified by 0.0.0.0 at 3-1-93 00:40:06
* Local updater ID is 0.0.0.0 (no valid interface found)
### Настроим S1 и S3 в качестве клиентов VTP <a name="VTP1"></a>
* Switch(config)#**vtp domain CCNA**
* Domain name already set to CCNA.
* Switch(config)#**vtp mode client**
* Setting device to VTP Client mode for VLANS.
* Switch(config)#**vtp password cisco**
* Password already set to cisco
* Switch#**sh vtp status** 
* VTP Version capable             : 1 to 3
* VTP version running             : 1
* VTP Domain Name                 : CCNA
* VTP Pruning Mode                : Disabled
* VTP Traps Generation            : Disabled
* Device ID                       : 0008.302d.4580
* Configuration last modified by 0.0.0.0 at 3-1-93 00:40:06 
* Feature VLAN:
* VTP Operating Mode                : Client
* Maximum VLANs supported locally   : 255
* Number of existing VLANs          : 9
* Configuration Revision            : 8
* MD5 digest                        : 0x16 0x68 0xF2 0x0B 0x67 0x31 0x81 0x1B 
                                      0x34 0xDB 0xE5 0xB5 0x68 0x63 0x5A 0x70

## Настроим динамический протокол транкинг (DTP)<a name="DTP"></a>
### Между коммутаторами S1 и S3 установим статический магистральный канал с помощью команды switchport mode trunk в режиме интерфейсной настройки для порта F0/3
* Switch(config)#**interface f0/3**
* Switch(config-if)# **switchport mode dynamic desirable**
### Проверим магистрали с помощью команды show interfaces trunk на коммутаторе S1.
* Switch#**sh interfaces trunk** 
* Port        Mode             Encapsulation  Status        Native vlan
* Fa0/1       auto             802.1q         trunking      1
* Fa0/2       auto             802.1q         trunking      1
* Fa0/3       auto             802.1q         trunking      1
* Fa0/4       auto             802.1q         trunking      1
* Port        Vlans allowed on trunk
* Fa0/1       1-4094
* Fa0/2       1-4094
* Fa0/3       1-4094
* Fa0/4       1-4094
### Настроим постоянную магистраль между коммутаторами S2 и S3.
* Switch(config)#**interface f0/1**
* Switch(config-if)#**switchport mode trunk**
* Switch#sh interfaces trunk
* Port        Mode         Encapsulation  Status        Native vlan
* Fa0/1       on           802.1q         trunking      1
* Fa0/2       desirable    802.1q         trunking      1
* Fa0/3       auto         802.1q         trunking      1
* Fa0/4       desirable    802.1q         trunking      1
### Настройка динамические магистральные каналы между S1 и S2<a name="DTP1"></a>
* Switch(config)#**interface f0/3**
* Switch(config-if)#**switchport mode dynamic desirable**
* Switch#**sh interfaces trunk**
* Port        Mode             Encapsulation  Status        Native vlan
* Fa0/1       auto             802.1q         trunking      1
* Fa0/2       auto             802.1q         trunking      1
* Fa0/3       desirable        802.1q         trunking      1
* Fa0/4       auto             802.1q         trunking      1
* Port        Vlans allowed on trunk
* Fa0/1       1-4094
* Fa0/2       1-4094
* Fa0/3       1-4094
* Fa0/4       1-4094

### Настройка статический магистральный канал между S1 и S3<a name="DTP2"></a>
* Switch(config)#**interface f0/1**
* Switch(config-if)#**switchport mode trunk**
* Switch#**sh interfaces trunk**
* Port        Mode             Encapsulation  Status        Native vlan
* Fa0/1       on               802.1q         trunking      1
* Fa0/2       auto             802.1q         trunking      1
* Fa0/3       desirable        802.1q         trunking      1
* Fa0/4       auto             802.1q         trunking      1
* Port        Vlans allowed on trunk
## Добавим сеть VLAN и назначение портов<a name="VLAN"></a>
### На коммутаторе S1 добавим сеть VLAN 10
* S1(config)# **vlan 10**
### Таблица наименовай VLAN
| VLAN               | Имя          | 
|-------------------:|:-------------|
| 10                 | Red          |   
| 20                 | Blue         |  
| 30                 | Yellow       | 
| 99                 | Management   |  
### На коммутаторе S2 добавляем VLAN ориентируясь на таблицу<a name="VLAN1"></a>
* S2(config)# **vlan 10**
* S2(config-vlan)# **name Red**
* S2(config-vlan)# **vlan 20**
* S2(config-vlan)# **name Blue**
* S2(config-vlan)# **vlan 30**
* S2(config-vlan)# **name Yellow**
* S2(config-vlan)# **vlan 99**
* S2(config-vlan)# **name Management**
* S2(config-vlan)# **end**

* S2# **show vlan brief**
* VLAN Name                             Status    Ports
* ---- -------------------------------- --------- -------------------------------
* 1    default                          active    Fa0/2, Fa0/4, Fa0/5, Fa0/6
*                                                 Fa0/7, Fa0/8, Fa0/9, Fa0/10
*                                                 Fa0/11, Fa0/12, Fa0/13, Fa0/14
*                                                 Fa0/15, Fa0/16, Fa0/17, Fa0/18
*                                                 Fa0/19, Fa0/20, Fa0/21, Fa0/22
*                                                 Fa0/23, Fa0/24, Gi0/1, Gi0/2
* 10   Red                              active
* 20   Blue                             active
* 30   Yellow                           active
* 99   Management                       active

### Проверим обновление VTP на коммутаторах S1 и S3<a name="VLAN2"></a>
#### Для S1
* Switch#**sh vlan brief** 
* VLAN Name                             Status    Ports
* 1    default                          active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
*                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
*                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
*                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
*                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
*                                                Gi0/1, Gi0/2
* 10   Red                              active    
* 20   Blue                             active    
* 30   Yellow                           active    
* 99   Management                       active    
* 1002 fddi-default                     act/unsup 
* 1003 token-ring-default               act/unsup 
* 1004 fddinet-default                  act/unsup 
* 1005 trnet-default                    act/unsup
#### Для S3
* Switch#**sh vlan brief**
* VLAN Name                             Status    Ports

* 1    default                          active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
*                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
*                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
*                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
*                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
*                                                Gi0/1, Gi0/2
* 10   Red                              active    
* 20   Blue                             active    
* 30   Yellow                           active    
* 99   Management                       active    
* 1002 fddi-default                     act/unsup 
* 1003 token-ring-default               act/unsup 
* 1004 fddinet-default                  act/unsup 
* 1005 trnet-default                    act/unsup 

### Накинем VLAN на порты
| Назначение портов    | VLAN      | IP-адрес и префикс прикрпленного компьютера   |  
|---------------------:|:----------|----------------------------------------------:|
| S1 F0/6              | VLAN 10   | 192.168.99.1                                  |
| S2 F0/18             | VLAN 20   | 192.168.99.2                                  |
| S3 F0/18             | VLAN 10   | 192.168.99.3                                  |

* S1(config)# interface f0/6
* S1(config-if)# switchport mode access
* S1(config-if)# switchport access vlan 10 
