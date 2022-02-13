# Домашнее задание 3.7 Компьютерные сети часть 2
## 1.
        ip -c -br l
        lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP> 
        enp3s0           DOWN           74:46:a0:b8:1c:c9 <NO-CARRIER,BROADCAST,MULTICAST,UP> 
        wlx7898e8c1c8b3  UP             78:98:e8:c1:c8:b3 <BROADCAST,MULTICAST,UP,LOWER_U

        ip a
        1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
        2: enp3s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
        link/ether 74:46:a0:b8:1c:c9 brd ff:ff:ff:ff:ff:ff
        10: wlx7898e8c1c8b3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 78:98:e8:c1:c8:b3 brd ff:ff:ff:ff:ff:ff
        inet 192.168.88.238/24 brd 192.168.88.255 scope global dynamic noprefixroute wlx7898e8c1c8b3
       valid_lft 304sec preferred_lft 304sec
        inet6 fe80::f3a5:1bb:cb78:a5fc/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
## 2.
        sudo apt install lldpd

        lldpctl
        -------------------------------------------------------------------------------
        LLDP neighbors:
        -------------------------------------------------------------------------------
        Interface:    wlx7898e8c1c8b3, via: LLDP, RID: 5, Time: 0 day, 00:01:06
        Chassis:     
        ChassisID:    mac 08:55:31:3c:8a:4a
        SysName:      MikroTik
        SysDescr:     MikroTik RouterOS 6.45.9 (long-term) RB941-2nD
        MgmtIP:       192.168.88.1
        Capability:   Bridge, on
        Capability:   Router, on
        Capability:   Wlan, on
        Port:        
        PortID:       ifname bridge/wlan1
        TTL:          120
        -------------------------------------------------------------------------------
## 3.
VLANs – это виртуальные сети, которые существуют на втором уровне модели OSI. То есть, VLAN можно настроить на коммутаторе второго уровня. Если смотреть на VLAN, абстрагируясь от понятия «виртуальные сети», то можно сказать, что VLAN – это просто метка в кадре, который передается по сети. Метка содержит номер VLAN (его называют VLAN ID или VID), – на который отводится 12 бит, то есть, вилан может нумероваться от 0 до 4095. Первый и последний номера зарезервированы, их использовать нельзя. Обычно, рабочие станции о VLAN ничего не знают (если не конфигурировать VLAN на карточках специально). О них думают коммутаторы. На портах коммутаторов указывается в каком VLAN они находятся. В зависимости от этого весь трафик, который выходит через порт помечается меткой, то есть VLAN. Таким образом каждый порт имеет PVID (port vlan identifier).Этот трафик может в дальнейшем проходить через другие порты коммутатора(ов), которые находятся в этом VLAN и не пройдут через все остальные порты. В итоге, создается изолированная среда (подсеть), которая без дополнительного устройства (маршрутизатора) не может взаимодействовать с другими подсетями.

Установка

        $ sudo apt install vlan
Существует команда 

        vconfig add eth0 5

Она позволяет вам создавать и удалять устройства VLAN в ядре с поддержкой VLAN.
Конфиг файл находится 

        sudo  nano /etc/network/interfaces

Пример

        auto eth0.100
        iface eth0.100 inet static
        address 192.168.1.200
        netmask 255.255.255.0
        vlan-raw-device eth0

## 4.
>> Mode-0(balance-rr)  Balance-rr обеспечивается балансировку нагрузки и отказоустойчивость

>> Mode-1(active-backup) 

>>Mode-2(balance-xor)

>>Mode-3(broadcast)

>> Mode-4(802.3ad)

>> Mode-5(balance-tlb)

>> Mode-6(balance-alb)

        sudo apt install ifenslave
Этот пакет дает возможность задавать параметры для bond интерфейса в файле «/etc/network/interfaces». Параметры задаются в виде: «bond-option value« (например: bond-mode active-backup).

Листинг

        auto bond0
        iface bond0 inet dhcp
        bond-slaves eth0 eth1
        bond-mode active-backup
        bond-miimon 100
        bond-primary eth0 eth1 

## 5.
        ipcalc 192.168.0.0/29
        Address:   192.168.0.0          11000000.10101000.00000000.00000 000
        Netmask:   255.255.255.248 = 29 11111111.11111111.11111111.11111 000
        Wildcard:  0.0.0.7              00000000.00000000.00000000.00000 111
        =>
        Network:   192.168.0.0/29       11000000.10101000.00000000.00000 000
        HostMin:   192.168.0.1          11000000.10101000.00000000.00000 001
        HostMax:   192.168.0.6          11000000.10101000.00000000.00000 110
        Broadcast: 192.168.0.7          11000000.10101000.00000000.00000 111
        Hosts/Net: 6                     Class C, Private In

42 подсети можно получить из сети с маской /24

        ipcalc -b --split 6 6 192.168.1.1/24
        Address:   192.168.1.1          
        Netmask:   255.255.255.0 = 24   
        Wildcard:  0.0.0.255            
        =>
        Network:   192.168.1.0/24       
        HostMin:   192.168.1.1          
        HostMax:   192.168.1.254        
        Broadcast: 192.168.1.255        
        Hosts/Net: 254                   Class C, Private Internet

        1. Requested size: 6 hosts
        Netmask:   255.255.255.248 = 29 
        Network:   192.168.1.0/29       
        HostMin:   192.168.1.1          
        HostMax:   192.168.1.6          
        Broadcast: 192.168.1.7          
        Hosts/Net: 6                     Class C, Private Internet

        2. Requested size: 6 hosts
        Netmask:   255.255.255.248 = 29 
        Network:   192.168.1.8/29       
        HostMin:   192.168.1.9          
        HostMax:   192.168.1.14         
        Broadcast: 192.168.1.15         
        Hosts/Net: 6                     Class C, Private Internet
## 6.
По условиям задачи, я ограничем в выборе IP адресов 
Исходя из информации в сети, я могу выбрать только из этих 4 списков адресов
>> 10.0.0.0 — 10.255.255.255 (маска подсети для бесклассовой (CIDR) адресации: 255.0.0.0 или /8)

>> 100.64.0.0 — 100.127.255.255 (маска подсети 255.192.0.0 или /10) - Данная подсеть рекомендована согласно RFC 6598 для использования в качестве адресов для CGN (Carrier-Grade NAT).

>> 172.16.0.0 — 172.31.255.255 (маска подсети: 255.240.0.0 или /12)

>> 192.168.0.0 — 192.168.255.255 (маска подсети: 255.255.0.0 или /16)

        ipcalc 100.64.0.0/26
        Address:   100.64.0.0           01100100.01000000.00000000.00 000000
        Netmask:   255.255.255.192 = 26 11111111.11111111.11111111.11 000000
        Wildcard:  0.0.0.63             00000000.00000000.00000000.00 111111
        =>
        Network:   100.64.0.0/26        01100100.01000000.00000000.00 000000
        HostMin:   100.64.0.1           01100100.01000000.00000000.00 000001
        HostMax:   100.64.0.62          01100100.01000000.00000000.00 111110
        Broadcast: 100.64.0.63          01100100.01000000.00000000.00 111111
        Hosts/Net: 62                    Class  

## 7.
        arp -a
        _gateway (192.168.88.1) в 08:55:31:3c:8a:46 [ether] на wlx7898e8c1c8b3
        arp -d 192.168.88.* - удалить из таблицы ARP записи для диапазона IP-адресов 192.168.88.1 - 192.168.88.254
        arp -d 192.168.88.100 - удалить из таблицы ARP запись для IP-адреса 192.168.88.100
