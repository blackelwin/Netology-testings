# DevOps-Netology

## Домашнее задание к занятию "3.8. Компьютерные сети, лекция 3"

1. Готово:
```
route-views>show ip route 80.83.235.79   
Routing entry for 80.83.235.0/24
  Known via "bgp 6447", distance 20, metric 0
  Tag 3303, type external
  Last update from 217.192.89.50 3w6d ago
  Routing Descriptor Blocks:
  * 217.192.89.50, from 217.192.89.50, 3w6d ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 3303
      MPLS label: none
route-views>show bgp 80.83.235.79     
BGP routing table entry for 80.83.235.0/24, version 2540216420
Paths: (22 available, best #20, table default)
  Not advertised to any peer
  Refresh Epoch 1
  7660 4635 8359
    203.181.248.168 from 203.181.248.168 (203.181.248.168)
      Origin IGP, localpref 100, valid, external
      Community: 0:3216 0:4635 0:12389 0:20485 0:31133 4635:4635 4635:65022 7660:6 7660:9003 8359:5500 8359:55638
      unknown transitive attribute: flag 0xE0 type 0x20 length 0x24
        value 0000 121B 0000 0000 0000 0000 0000 121B
              0000 0001 0000 0000 0000 121B 0000 0001
              0000 FDFE 
      path 7FE0F6220BB0 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3561 3910 3356 8359
    206.24.210.80 from 206.24.210.80 (206.24.210.80)
      Origin IGP, localpref 100, valid, external
      path 7FE0C72188D0 RPKI State not found
      rx pathid: 0, tx pathid: 0
```
2. Готово:
Был поднят dummy интерфейс: 

`sudo ip link add name dummy0 type dummy` 

и созданы 2 файла: 

`sudo touch /etc/systemd/network/dummy0.netdev`

`sudo touch /etc/systemd/network/dummy0.network`

с таким наполнением `dummy0.network`:
```
[Match]
Name=dummy0

[Network]
Address=10.2.2.2
Mask=255.255.255.255
```
и таким наполнением `dummy0.netdev`:
```
[NetDev]
Name=dummy0
Kind=dummy
```
после была перезагружена сеть `sudo systemctl restart systemd-networkd`

и добавлены статические маршруты:

`route add -net 10.0.2.0/24 dummy0`

`route add -net 10.2.0.0/16 dummy0`

`route add -net 192.168.192.0/24 dummy0`

Ниже вывод таблицы маршрутизации:
```
vagrant@sysadm-fs:~$ ip route
default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100 
10.0.0.0/8 dev dummy0 proto kernel scope link src 10.2.2.2 
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15 
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100 
10.2.0.0/16 dev dummy0 scope link 
192.168.56.0/24 dev eth1 proto kernel scope link src 192.168.56.3 
192.168.192.0/24 dev dummy0 scope link 
```
3. Готово:
```
[Vagrant@Vagrant]$ sudo netstat -pnlt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:2222          0.0.0.0:*               LISTEN      53314/VBoxHeadless  
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/systemd           
tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN      5806/dnsmasq        
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1448/sshd           
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      1446/cupsd          
tcp        0      0 0.0.0.0:7070            0.0.0.0:*               LISTEN      5122/anydesk        
tcp6       0      0 :::111                  :::*                    LISTEN      1/systemd           
tcp6       0      0 :::22                   :::*                    LISTEN      1448/sshd           
tcp6       0      0 ::1:631                 :::*                    LISTEN      1446/cupsd          
```
4. Готово:
```
[Vagrant@Vagrant]$ sudo netstat -pnlu
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
udp        0      0 0.0.0.0:111             0.0.0.0:*                           1/systemd           
udp        0      0 127.0.0.1:323           0.0.0.0:*                           1102/chronyd        
udp        0      0 0.0.0.0:36167           0.0.0.0:*                           1068/avahi-daemon:  
udp        0      0 224.0.0.251:5353        0.0.0.0:*                           11430/chrome        
udp        0      0 224.0.0.251:5353        0.0.0.0:*                           11430/chrome        
udp        0      0 224.0.0.251:5353        0.0.0.0:*                           11477/chrome --type 
udp        0      0 0.0.0.0:5353            0.0.0.0:*                           1068/avahi-daemon:  
udp        0      0 192.168.122.1:53        0.0.0.0:*                           5806/dnsmasq        
udp        0      0 0.0.0.0:67              0.0.0.0:*                           5806/dnsmasq        
udp6       0      0 :::111                  :::*                                1/systemd           
udp6       0      0 ::1:323                 :::*                                1102/chronyd        
udp6       0      0 :::41371                :::*                                1068/avahi-daemon:  
udp6       0      0 :::5353                 :::*                                1068/avahi-daemon:  
```
