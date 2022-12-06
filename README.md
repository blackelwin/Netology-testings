# DevOps-Netology

## Домашнее задание к занятию "3.5. Файловые системы"

1. Изучил. Интересное решение, позволяющее экономить дисковое пространство, увеличивая скорость записи и срок службы запоминающего устройства. Но есть свои определенные нюансы в работе с sparse файлами:

 - ограничение в виде поддерживаемых ФС и ПО;

 - фрагментрация файла при частой записи данных в дыры;

 - невозможность записи данных в дыры при отсутствии свободного места на диске и использования других индикаторов дыр, кроме нулевых байт.

2. Hardlink не могут иметь разные права доступа и владельцев потому, что это ссылки на один и тот же объект и имеют одинаковую Inode.

3. Готово. Утилиты `mdadm`, `fdisk`, `sfdisk`, `mkfs`, `lsblk`, `wget` были уже предустановлены. Вывод команды `lsblk`:
```
vagrant@sysadm-fs:~$ lsblk
NAME               MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                7:0    0 67.2M  1 loop /snap/lxd/21835
loop1                7:1    0 43.6M  1 loop /snap/snapd/14978
loop2                7:2    0 61.9M  1 loop /snap/core20/1328
sda                  8:0    0   64G  0 disk 
├─sda1               8:1    0    1M  0 part 
├─sda2               8:2    0  1.5G  0 part /boot
└─sda3               8:3    0 62.5G  0 part 
  └─ubuntu--vg-ubuntu--lv
                   253:0    0 31.3G  0 lvm  /
sdb                  8:16   0  2.5G  0 disk 
sdc                  8:32   0  2.5G  0 disk 
```
4. Готово:
```
Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1          2048 4196351 4194304    2G 83 Linux
/dev/sdb2       4196352 5242879 1046528  511M 83 Linux
```
5. Готово:
```
vagrant@sysadm-fs:~$ sudo sfdisk -d /dev/sdb | sudo sfdisk --force /dev/sdc
Checking that no-one is using this disk right now ... OK

Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Created a new DOS disklabel with disk identifier 0xb1e5de59.
/dev/sdc1: Created a new partition 1 of type 'Linux' and of size 2 GiB.
/dev/sdc2: Created a new partition 2 of type 'Linux' and of size 511 MiB.
/dev/sdc3: Done.

New situation:
Disklabel type: dos
Disk identifier: 0xb1e5de59

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1          2048 4196351 4194304    2G 83 Linux
/dev/sdc2       4196352 5242879 1046528  511M 83 Linux
```
6. Готово:
```
vagrant@sysadm-fs:~$ sudo mdadm --create --verbose /dev/md0 -l 1 -n 2 /dev/sd{b1,c1}
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
mdadm: size set to 2094080K
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```
7. Готово:
```
vagrant@sysadm-fs:~$ sudo mdadm --create --verbose /dev/md1 -l 0 -n 2 /dev/sd{b2,c2}
mdadm: chunk size defaults to 512K
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md1 started.
vagrant@sysadm-fs:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 67.2M  1 loop  /snap/lxd/21835
loop2                       7:2    0 61.9M  1 loop  /snap/core20/1328
loop3                       7:3    0 63.2M  1 loop  /snap/core20/1738
loop4                       7:4    0 49.6M  1 loop  /snap/snapd/17883
loop5                       7:5    0 91.9M  1 loop  /snap/lxd/24061
sda                         8:0    0   64G  0 disk  
├─sda1                      8:1    0    1M  0 part  
├─sda2                      8:2    0  1.5G  0 part  /boot
└─sda3                      8:3    0 62.5G  0 part  
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk  
├─sdb1                      8:17   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdb2                      8:18   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdc2                      8:34   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
```
8. Готово:
```
vagrant@sysadm-fs:~$ sudo pvcreate /dev/md0 /dev/md1
  Physical volume "/dev/md0" successfully created.
  Physical volume "/dev/md1" successfully created.
```
9. Готово:
```
vagrant@sysadm-fs:~$ sudo vgcreate my-vg /dev/md0 /dev/md1
  Volume group "my-vg" successfully created
```
10. Готово: 
```
vagrant@sysadm-fs:~$ sudo lvcreate -L 100M my-vg /dev/md1
  Logical volume "lvol0" created.
```
11. Готово:
```
vagrant@sysadm-fs:~$ sudo mkfs.ext4 /dev/my-vg/lvol0
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
```
12. Готово:
```
vagrant@sysadm-fs:~$ sudo mount /dev/my-vg/lvol0 /tmp/new
```
13. Готово:
```
vagrant@sysadm-fs:~$ sudo wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
--2022-12-06 11:10:32--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 23814102 (23M) [application/octet-stream]
Saving to: ‘/tmp/new/test.gz’

/tmp/new/test.gz                      100%[=======================================================================>]  22.71M  5.67KB/s    in 65m 19s 

2022-12-06 12:15:53 (5.93 KB/s) - ‘/tmp/new/test.gz’ saved [23814102/23814102]
```
14. Готово:
```
vagrant@sysadm-fs:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 67.2M  1 loop  /snap/lxd/21835
loop2                       7:2    0 61.9M  1 loop  /snap/core20/1328
loop3                       7:3    0 63.2M  1 loop  /snap/core20/1738
loop4                       7:4    0 49.6M  1 loop  /snap/snapd/17883
loop5                       7:5    0 91.9M  1 loop  /snap/lxd/24061
sda                         8:0    0   64G  0 disk  
├─sda1                      8:1    0    1M  0 part  
├─sda2                      8:2    0  1.5G  0 part  /boot
└─sda3                      8:3    0 62.5G  0 part  
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk  
├─sdb1                      8:17   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdb2                      8:18   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
    └─my--vg-lvol0        253:1    0  100M  0 lvm   /tmp/new
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdc2                      8:34   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
    └─my--vg-lvol0        253:1    0  100M  0 lvm   /tmp/new
```
15. Готово:
```
vagrant@sysadm-fs:~$ gzip -t /tmp/new/test.gz
vagrant@sysadm-fs:~$ echo $?
0
```
16. Готово:
```
vagrant@sysadm-fs:~$ sudo pvmove /dev/md1
  /dev/md1: Moved: 16.00%
  /dev/md1: Moved: 100.00%
vagrant@sysadm-fs:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 67.2M  1 loop  /snap/lxd/21835
loop2                       7:2    0 61.9M  1 loop  /snap/core20/1328
loop3                       7:3    0 63.2M  1 loop  /snap/core20/1738
loop4                       7:4    0 49.6M  1 loop  /snap/snapd/17883
loop5                       7:5    0 91.9M  1 loop  /snap/lxd/24061
sda                         8:0    0   64G  0 disk  
├─sda1                      8:1    0    1M  0 part  
├─sda2                      8:2    0  1.5G  0 part  /boot
└─sda3                      8:3    0 62.5G  0 part  
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk  
├─sdb1                      8:17   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
│   └─my--vg-lvol0        253:1    0  100M  0 lvm   /tmp/new
└─sdb2                      8:18   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
│   └─my--vg-lvol0        253:1    0  100M  0 lvm   /tmp/new
└─sdc2                      8:34   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
```
17. Готово:
```
vagrant@sysadm-fs:~$ sudo mdadm /dev/md0 --fail /dev/sdb1
mdadm: set /dev/sdb1 faulty in /dev/md0
```
18. Готово:
```
vagrant@sysadm-fs:~$ dmesg | grep md0
[ 2415.395036] md/raid1:md0: not clean -- starting background reconstruction
[ 2415.395038] md/raid1:md0: active with 2 out of 2 mirrors
[ 2415.395060] md0: detected capacity change from 0 to 2144337920
[ 2415.399460] md: resync of RAID array md0
[ 2425.591188] md: md0: resync done.
[ 8419.895227] md/raid1:md0: Disk failure on sdb1, disabling device.
               md/raid1:md0: Operation continuing on 1 devices.
```
19. Готово:
```
vagrant@sysadm-fs:~$ gzip -t /tmp/new/test.gz
vagrant@sysadm-fs:~$ echo $?
0
```
20. Готово, машина погашена 
