# DevOps-Netology

## Домашнее задание к занятию "3.7. Компьютерные сети.Лекция 2"

1. В windows стандартная команда в cmd -  `ipconfig`. В linux - `ifconfig` и `ip a` (ifconfig в некоторых сборках нужно устанавливать отдельно):
```
vagrant@sysadm-fs:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:a2:6b:fd brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 86387sec preferred_lft 86387sec
    inet6 fe80::a00:27ff:fea2:6bfd/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:27:6b:96 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.3/24 brd 192.168.56.255 scope global dynamic eth1
       valid_lft 587sec preferred_lft 587sec
    inet6 fe80::a00:27ff:fe27:6b96/64 scope link 
       valid_lft forever preferred_lft forever
vagrant@sysadm-fs:~$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
        inet6 fe80::a00:27ff:fea2:6bfd  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:a2:6b:fd  txqueuelen 1000  (Ethernet)
        RX packets 820  bytes 101409 (101.4 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 691  bytes 114812 (114.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.56.3  netmask 255.255.255.0  broadcast 192.168.56.255
        inet6 fe80::a00:27ff:fe27:6b96  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:27:6b:96  txqueuelen 1000  (Ethernet)
        RX packets 8  bytes 3660 (3.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 23  bytes 3629 (3.6 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
2. Для распознавания соседних устройств в сети используется протокол LLDP (CDP - cisco). В linux есть пакет lldpd, после установки которого командой `lldpctl` можно посмотреть информацию о соседних устройствах.2. Для распознавания соседних устройств в сети используется протокол LLDP (CDP - cisco). В linux есть пакет lldpd, после установки которого командой `lldpctl` можно посмотреть информацию о соседних устройствах.

3. Для разделения сетей на подсети используется технология VLAN. Вопрос считаю крайне некорректным и подачу материала тоже. Разделением на подсети занимается L3 коммутаторы и маршрутизаторы, это уровень L3 и лишь там в заголовках пакетов появляется IP адреса и разбитие адресов на подсети. На уровне L2 - не пакеты, а кадры, в заголовках которых MAC адреса. Коммутаторы L2 работают лишь в одном широковещательном домене и лишь с MAC адресами, IP адреса им безразличны, соответственно и безразлично разбиение на подсети.

В Linux есть много различных пакетов, например `vlan`, но так же это можно реализовать стандартными средствами. Важна поддержка сетевой карты и установленного драйвера. Стандартными средствами, например в Ubuntu, редактируется файл `/etc/network/interfaces`, где физический интерфейс, например `eth0` "делится" на subinterface (схожее с cisco) eth0.2. В файл `/etc/network/interfaces` добавляется дополнительно информация о сабинтерфейсе:
```
auto eth0.2
iface eth0.2 inet static
address 192.168.1.2
netmask 255.255.255.0
vlan-raw-device eth0
```
После файл сохраняется и необходимо перезагрузить сеть командой `systemctl restart network`

4. mode=0 (balance-rr)
Этот режим используется по-умолчанию, если в настройках не указано другое. balance-rr обеспечивает балансировку нагрузки и отказоустойчивость. В данном режиме пакеты отправляются "по кругу" от первого интерфейса к последнему и сначала. Если выходит из строя один из интерфейсов, пакеты отправляются на остальные оставшиеся.При подключении портов к разным коммутаторам, требует их настройки.

mode=1 (active-backup)
При active-backup один интерфейс работает в активном режиме, остальные в ожидающем. Если активный падает, управление передается одному из ожидающих. Не требует поддержки данной функциональности от коммутатора.

mode=2 (balance-xor)
Передача пакетов распределяется между объединенными интерфейсами по формуле ((MAC-адрес источника) XOR (MAC-адрес получателя)) % число интерфейсов. Один и тот же интерфейс работает с определённым получателем. Режим даёт балансировку нагрузки и отказоустойчивость.

mode=3 (broadcast)
Происходит передача во все объединенные интерфейсы, обеспечивая отказоустойчивость.

mode=4 (802.3ad)
Это динамическое объединение портов. В данном режиме можно получить значительное увеличение пропускной способности как входящего так и исходящего трафика, используя все объединенные интерфейсы. Требует поддержки режима от коммутатора, а так же (иногда) дополнительную настройку коммутатора.

mode=5 (balance-tlb)
Адаптивная балансировка нагрузки. При balance-tlb входящий трафик получается только активным интерфейсом, исходящий - распределяется в зависимости от текущей загрузки каждого интерфейса. Обеспечивается отказоустойчивость и распределение нагрузки исходящего трафика. Не требует специальной поддержки коммутатора.

mode=6 (balance-alb)
Адаптивная балансировка нагрузки (более совершенная). Обеспечивает балансировку нагрузки как исходящего (TLB, transmit load balancing), так и входящего трафика (для IPv4 через ARP). Не требует специальной поддержки коммутатором, но требует возможности изменять MAC-адрес устройства.

Для настройки агрегации портов можно использовать пакет ifenslave. Пример настройки файла `/etc/network/interfaces`:
```
auto bond0 eth0 eth1
iface bond0 inet static
        address 10.0.0.11
        netmask 255.255.255.0
        gateway 10.0.0.254
        bond-slaves eth0 eth1
        bond-mode balance-alb
bond-miimon 100
bond-downdelay 200
        bond-updelay 200
```
5. Всего в маске /29 8 IP адресов, из которых можно использовать 6. Всего можно получить 32 подсети с маской /29 из сети с маской /24. Примеры:

10.10.10.0/29 - первая подсеть

10.10.10.8/29 - вторая подсеть

10.10.10.16/29 - третья подсеть

10.10.10.24/29 - четвертая подсеть и так далее.

6. Для объединения можно использовать выделенный для Carrier-Grade NAT диапазон, IANA он разрешен и для использования в частных сетях. Пример сети:

100.64.0.0/27 - 32 всего адреса, доступных 30 или же 100.64.0.26/ - 64 всего адреса, 62 доступно.

Диапазон в 40-50 сделать не получится, ограничено маской подсети, не корректная постановка вопроса, либо же ограничить построенную на /26 маске сеть средствами DHCP сервера и/или ACL маршрутизатора/коммутатора.

7. В windows cmd это команда `arp -a`. В Linux - `ip neigh`. Чтобы очистить всю ARP таблицу можно использовать команду `ip neigh flush all`, а какое то конкретное устройство - `ip neigh flush dev <MAC>`, либо IP - `ip neigh flush to <IP-addr>`

