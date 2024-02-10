University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [IP-telephony](https://github.com/itmo-ict-faculty/ip-telephony)
Year: 2023/2024
Group: K34212
Author: Spevak Elena
Lab: Lab1
Date of create: 10.02.2024
Date of finished: 13.02.2024

# Лабораторная работа №1
# "Базовая настройка IP-телефонов в среде Сisco packet tracer"

**Цель работы** - изучить рабочую среду Cisco Packet Tracer, ознакомиться с интерфейсами основных устройств, типами кабелей, научиться собирать топологию; изучить построение сети IP-телефонии с помощью маршрутизатора, коммутатора и IP телефонов Cisco 7960 в среде Packet tracer.

**Ход работы:**

   Часть 1

В среде Cisco Packet Tracer была собрана следующая схема соединения.

![Схема 1](lab1\images\IP-tel_lab1_schema1.drawio.png)

На ней были четыре коммутатора, к которым были подключены семь компьютеров. Для соединения коммутаторов был использован кабель медная витая пара (copper cross-over). Для подлкючения комьютеров был использован кабель прямого типа (copper straight-through). Компьютерам были даны статические адреса из сети 192.168.10.0 под маской 255.255.255.0 (192.168.10.1-192.168.10.7 соответсвенно). Для проверки связанности комьютеров использовались ping запросы.
Примеры проверки связанности представлены ниже.

![Связанность 1 и 7](lab1\images\ping 1.png)

![Связанность 3 и 5](lab1\images\ping 2.png)

Часть 2.

1. Была собрана вторая схема, представленная на рисунке ниже

![Схема 2](lab1\images\IP-tel_lab1_schema2.drawio.png)

2.  На ней с помощью команды  ```# hostname CMERouter``` было изменено имя маршрутизатора. 
3.  На нем же был настроен интерфейс fa0/0:

```
CMERouter(config)#int fa0/0
CMERouter(config)#ip address 192.168.1.1 255.255.255.0
CMERouter(config)#no shutdown
```

На данный интерфейс был установлен адресс 192.168.1.1/24, после чего сам интерфейс был поднят.

4. На маршрутизаторе был настроен DHCP сервер для передачи голова и данных.

Для этого был создан пул DHCP адресов с названием IPTel, в который входила сеть 192.168.1.0/24. Был указан адрес маршрута по умолчанию - адресс интерфейса маршрутизатора. Для передачи голоса была подключена опция 150, которая позволяет использовать CallManager Express ```CMERouter(dhcp-config)#option 150 ip 192.168.1.1```.

5. Была произведена настройка услуги Cisco CallManager Express на маршрутизаторе.

Для телефонного сервиса был настроен автоматический режим, в котором ведется диалоговый обмен сообщениями. Для этого в конфигурируемом режиме были заданы максимальное колчиество номеров, присваемое IP-телефонам, и максимальное количество самих IP-телефонов - 5 шт. Также был указан голосовой шлюз - 2000 порт интерфейса маршрутизатора 192.168.1.1 и настроено автоматическое назначение внешних номеров.

6. Была произведена настройка коммутатора

На коммутаторе были настроены порты - переведены в режим access, и через них проброшен голосовой канал.

7. Настройка Ip-телефонов

При настройке телефонов была создана телефонная линия. Для этого каждому телефону был дан свой номер в выделенной линии (54001 и 54002).

Для проверки связанности телефонов был произвден звонок:

![Звонок с телефона на телефон](lab1\images\cheking_calls.png)

Конфигурационный файл маршрутизатора предсталвен ниже:

```
CMERouter#
CMERouter#show running-config 
Building configuration...

Current configuration : 1109 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname CMERouter
!
!
ip dhcp pool Phone
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 option 150 ip 192.168.1.1
!
!
!ip cef
no ipv6 cef
!
license udi pid CISCO2811/K9 sn FTX10179N69-
!
!
no ip domain-lookup
!
!
spanning-tree mode pvst
!
!
interface FastEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet0/1
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
!
ip flow-export version 9
!
!
telephony-service
 max-ephones 5
 max-dn 5
 ip source-address 192.168.1.1 port 2000
 auto assign 4 to 6
 auto assign 1 to 5
!
ephone-dn 1
 number 54001
!
ephone-dn 2
 number 54002
!
ephone 1
 device-security-mode none
 mac-address 0006.2A5C.60C1
 type 7960
 button 1:1
!
ephone 2
 device-security-mode none
 mac-address 0009.7C6A.9911
 type 7960
 button 1:2
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
end
```
**Вывод:** была изучена рабочая среда Cisco Packet Tracer, в которой были собраны две топологии. Была построена сеть IP-телефонии с помощью маршрутизатора, коммутатора и IP-телефонов и проверена ее работоспособность.

