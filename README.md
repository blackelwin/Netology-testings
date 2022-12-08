# DevOps-Netology

## Домашнее задание к занятию "3.8. Компьютерные сети, лекция 3"

5. Готово:

Сгенерировал пару rsa ключей:
```
vagrant@sysadm-fs:~$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa): new1
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in new1
Your public key has been saved in new1.pub
The key fingerprint is:
SHA256:ItcMWo9hLIuMFJwLdAw3jXWrNJVZp6xoY/YrBFqAtnA vagrant@sysadm-fs
The key's randomart image is:
+---[RSA 3072]----+
|+++++. o+. .     |
|+=Eo.oooo o      |
|+o+ .o*. o       |
|.= +.*+B.        |
|. = =B+.S        |
| .  ++o.         |
|    .  .         |
|     .  .        |
|      ..         |
+----[SHA256]-----+
```
Скопировал публичный ключ на удаленный сервер и подключился:
```vagrant@sysadm-fs:~$ ssh-copy-id -i new1 blackelwin@192.168.56.1
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "new1.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
blackelwin@192.168.56.1's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'blackelwin@192.168.56.1'"
and check to make sure that only the key(s) you wanted were added.

vagrant@sysadm-fs:~$ ssh blackelwin@192.168.56.1
blackelwin@192.168.56.1's password: 
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Tue Dec  6 15:15:42 2022
[blackelwin@DevOps ~]$ pwd
/home/blackelwin
```
6. Готово:

Переименовал и переместил ключи из задания 5:
```
vagrant@sysadm-fs:~$ mv new1 .ssh/new2.key
vagrant@sysadm-fs:~$ mv new1.pub .ssh/new2.pub
```
Создал и отредактировал файл конфигурации:
```
vagrant@sysadm-fs:~$ mkdir -p ~/.ssh && chmod 700 ~/.ssh
vagrant@sysadm-fs:~$ touch ~/.ssh/config && chmod 600 ~/.ssh/config
```
Host my_serv  
HostName 192.168.56.1
IdentityFile ~/.ssh/new2.key
User blackelwin
Host *
        User blackelwin
        IdentityFile ~/.ssh/new2.pub
        Protocol 2
```
Теперь идет подключение на удаленный сервер по имени сервера:
```
vagrant@sysadm-fs:~$ ssh my_serv
Enter passphrase for key '/home/vagrant/.ssh/new2.key': 
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Thu Dec  8 16:43:50 2022 from 192.168.56.3
[blackelwin@DevOps ~]$ exit
```
