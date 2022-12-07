# DevOps-Netology

## Домашнее задание к занятию "3.6. Компьютерные сети. Лекция 1"

1. Готово:
```
vagrant@sysadm-fs:~$ telnet stackoverflow.com 80
Trying 151.101.193.69...
Connected to stackoverflow.com.
Escape character is '^]'.

GET /questions HTTP/1.0
HOST: stackoverflow.com

HTTP/1.1 403 Forbidden
Connection: close
Content-Length: 1920
Server: Varnish
Retry-After: 0
Content-Type: text/html
Accept-Ranges: bytes
Date: Wed, 07 Dec 2022 02:15:30 GMT
Via: 1.1 varnish
X-Served-By: cache-fra-eddf8230139-FRA
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1670379330.140938,VS0,VE1
X-DNS-Prefetch-Control: off
```

Код HTTP 403 - client error, если более точно - доступ запрещен

2. Готово:

Был получен код 200 - success (успешное подключение)

Дольше всего обрабатывался запрос на сервере `waiting for server response` большей расшифровки не увидел.

3. Мой IP: 80.83.235.79

4. Готово:
```
descr:          Mobile TeleSystems, PJSC, MR DV
origin:         AS8359
```
5. Публичный сервер DNS google 8.8.8.8 у меня заблокирован. Но вот вывод с 1.1.1.1 (CloudFlare):
```
vagrant@sysadm-fs:~$ traceroute -An 1.1.1.1
traceroute to 1.1.1.1 (1.1.1.1), 30 hops max, 60 byte packets
 7  82.204.206.193 [AS8359]  204.857 ms  207.202 ms  206.283 ms
 8  212.248.28.245 [AS8359]  206.593 ms  208.443 ms  207.804 ms
 9  212.248.28.246 [AS8359]  206.119 ms  207.473 ms  208.000 ms
10  212.188.31.24 [AS8359]  206.657 ms  209.940 ms  208.765 ms
11  195.34.50.182 [AS8359]  208.427 ms  209.004 ms  206.097 ms
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  1.1.1.1 [AS13335]  204.807 ms * *
```
Первые 5 строк вывода удалил - там конфиденциальная информация о сети, не могу раскрывать.

6. Готово:
```
 7. AS8359   82.204.206.193                                                                                 0.0%    34  128.8 202.3 126.6 355.1  78.4
 8. AS8359   212.248.28.245                                                                                 0.0%    34  127.7 195.1 126.2 335.1  68.2
 9. AS8359   212.248.28.246                                                                                 0.0%    34  125.9 201.4 125.9 350.5  74.9
10. AS8359   212.188.31.24                                                                                  0.0%    34  129.0 202.4 126.4 357.3  76.1
11. AS8359   195.34.50.182                                                                                  0.0%    34  131.4 205.2 126.4 352.4  78.9
12. AS8359   212.188.33.197                                                                                 0.0%    34  131.7 200.6 127.5 357.4  74.8
13. AS13335  1.1.1.1                                                                                        0.0%    34  130.5 193.0 126.7 340.6  66.2
```
Опять же не могу детально раскрыть всю информацию.

7. Готово:
```
vagrant@sysadm-fs:~$ dig dns.google

; <<>> DiG 9.16.1-Ubuntu <<>> dns.google
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 23781
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;dns.google.			IN	A

;; ANSWER SECTION:
dns.google.		808	IN	A	8.8.8.8
dns.google.		808	IN	A	8.8.4.4

;; Query time: 235 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Wed Dec 07 03:15:06 UTC 2022
;; MSG SIZE  rcvd: 71
```
В секции Answer описаны A записи - 8.8.8.8 и 8.8.4.4

8. Готово:
```
vagrant@sysadm-fs:~$ dig -x 8.8.8.8

; <<>> DiG 9.16.1-Ubuntu <<>> -x 8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 21031
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;8.8.8.8.in-addr.arpa.		IN	PTR

;; ANSWER SECTION:
8.8.8.8.in-addr.arpa.	69499	IN	PTR	dns.google.

;; Query time: 135 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Wed Dec 07 03:20:32 UTC 2022
;; MSG SIZE  rcvd: 73

vagrant@sysadm-fs:~$ dig -x 8.8.4.4

; <<>> DiG 9.16.1-Ubuntu <<>> -x 8.8.4.4
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 31747
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;4.4.8.8.in-addr.arpa.		IN	PTR

;; ANSWER SECTION:
4.4.8.8.in-addr.arpa.	71958	IN	PTR	dns.google.

;; Query time: 303 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Wed Dec 07 03:20:40 UTC 2022
;; MSG SIZE  rcvd: 73
```
DNS у обоих адресов (8.8.8.8 и 8.8.4.4) - dns.google 
