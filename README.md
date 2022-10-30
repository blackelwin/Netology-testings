# DevOps-Netology

## Домашнее задание к занятию "3.3. Операционные системы. Лекция 2"

1. Создал `node_exporter.service` в котором поместил:
```
[Unit]
Description=node_exporter
[Service]
ExecStart=/home/vagrant/node_exporter-1.4.0.linux-amd64/node_exporter
KillMode=process
Restart=on-failure
[Install]
WantedBy=multi-user.target
```

   Созданный файл `node_exporter.service` переместил в `/etc/systemd/system/`:

   `mv node_exporter.service /etc/systemd/system/node_exporter.service`

   Разрешил и включил вновь созданный `.service` файл:

   `systemctl enable node_exporter.service`

   `systemctl start node_exporter`
   
   Node exporter корректно стартует, завершается и автоматически запускается после перезагрузки.
   
2. CPU:
```
node_cpu_seconds_total{cpu="0",mode="idle"} 965.92
node_cpu_seconds_total{cpu="1",mode="system"} 11.08
node_cpu_seconds_total{cpu="1",mode="user"} 9.53
process_cpu_seconds_total 0.74
```
   Диск:
```
node_disk_io_now{device="sda"} 0
node_disk_io_time_seconds_total{device="sda"} 8.8
node_disk_read_bytes_total{device="sda"} 4.32633856e+08
node_disk_written_bytes_total{device="sda"} 6.31808e+07
```
   Память:
```
node_memory_MemAvailable_bytes 6.94157312e+08
node_memory_MemFree_bytes 2.87158272e+08
node_memory_MemTotal_bytes 1.024069632e+09
node_memory_SwapFree_bytes 2.047864832e+09
node_memory_SwapTotal_bytes 2.047864832e+09
```
   Сеть:
```
node_network_receive_bytes_total{device="eth0"} 247118
node_network_transmit_bytes_total{device="eth0"} 409572
node_network_up{device="eth0"} 1
```
3. Готово. Дополнительно пробросил порты для node exporter
4. Судя по выводу `dmesg` в виртуальной машине:
```
vagrant@vagrant:~$ dmesg | grep virtual
[    0.001724] CPU MTRRs all blank - virtualized system.
[    0.042056] Booting paravirtualized kernel on KVM
[    0.207710] Performance Events: PMU not available due to virtualization, using software events only.
[    2.733434] systemd[1]: Detected virtualization oracle.
```
   можно догадаться, что система запущена на виртуальной машине.

   На хосте вывод совсем другой:
```
[    0.000000] Booting paravirtualized kernel on bare hardware
[    4.473370] input: Acer WMI hotkeys as /devices/virtual/input/input13
[    4.493124] SVM: LBR virtualization supported
```
5. `/sbin/sysctl -n fs.nr_open` - это системный лимит на количество открытых дескрипторов. Ему же соответствует файл `/proc/sys/fs/nr_open`

   ulimit -a и ulimit -aH покажет текущие "мягкие" и жесткие лимиты для пользователя. При помощи ulimit можно настроить мягкие лимиты до пределов жестких.
6. 
```
root@vagrant:/home/vagrant# unshare --fork --pid --mount-proc sleep 1h
root@vagrant:/home/vagrant# ps | grep sleep
   3174 pts/0    00:00:00 sleep
root@vagrant:/home/vagrant# nsenter --target 3174 --pid --mount
root@vagrant:/# ps
    PID TTY          TIME CMD
      1 pts/0    00:00:00 sleep
      2 pts/0    00:00:00 bash
     13 pts/0    00:00:00 ps
```
7. В сети нашел информацию, что это "форк-бомба"

   `:(){ :|:& };:` - обьявление функции с именем ':' которая вызывает сама себя в двух экземплярах.

   В `dmesg` обнаружил строку:
```
[ 5030.702324] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-19.scope
```
   Судя по всему, контроллер пидов отклоняет создание форков в связи с превышением количества (лимита).

   `ulimit -u N` - ограничит число процессов до N для пользователя
