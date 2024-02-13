#### University: [ITMO University](https://itmo.ru/ru/)
#### Faculty: [FICT](https://fict.itmo.ru)
#### Course: [IP-telephony](https://github.com/itmo-ict-faculty/ip-telephony)
#### Year: 2023/2024
#### Group: K34212
#### Author: Spevak Elena
#### Lab: Lab2
#### Date of create: 13.02.2024
#### Date of finished: 20.02.2024

# Лабораторная работа №2
# "Конфигурация voip в среде Сisco packet tracer"

**Цель работы** - изучить построение сети IP-телефонии с помощью маршрутизатора Cisco 2811, коммутатора Cisco catalyst 3560 и IP телефонов Cisco 7960.

**Ход работы:**

   Часть 1

В среде Cisco Packet tracer была реализована схема, изображенная ниже

![Схема 1](https://github.com/LenaSpevak/2023_2024-ip-telephony-k34212-spevak_e_a/blob/main/lab2/images/IP-telephonyLab2Schema1.drawio.png)

С помощью команды  ```# hostname CMERouter``` было изменено имя маршрутизатора. 

Так же на маршрутизаторе был отключен синтаксис ввода слов от DNS сервером, заданы пароли для защиты маршрутизатора как в удаленном режиме, так и в режиме консоли. Для этого были использованы следующие команды:

```
CMERouter(config)# no ip domain-lookup
CMERouter(config)# line vty 0 4
CMERouter(config-line)# password 12345
CMERouter(config-line)# login
CMERouter(config-line)# logging synchronous
CMERouter(config-line)# exit
CMERouter(config)# line console 0
CMERouter(config-line)# password 12345
CMERouter(config-line)# login
CMERouter(config-line)# logging synchronous
CMERouter(config-line)# exit

```

На маршрутизаторе был настроен интерфейс fa0/0: ip адресс 192.168.1.1/24. Помимо этого был настроен DHCP сервер для передачи голоса и данных. Для этого был задан dhcp pool, а для передачи голоса прописана опция 150 

```
CMERouter(config)# int fa0/0
CMERouter(config-if)#no shutdown
CMERouter(config-if)#ip address 192.168.1.1 255.255.255.0
CMERouter(config-if)#exit
CMERouter(config)#ip dhcp pool phones
CMERouter(dhcp-config)#network 192.168.1.0 255.255.255.0
CMERouter(dhcp-config)#default-router 192.168.1.1
CMERouter(dhcp-config)#option 150 ip 192.168.1.1

```

Для настройки телефонного сервиса были выполнены следующие команды:

```
CMERouter(config)#telephony-service
CMERouter(config-telephony)#max-dn 5
CMERouter(config-telephony)#max-ephones 5
CMERouter(config-telephony)#ip source-address 192.168.10.1 port 2000
CMERouter(config-telephony)#auto assign 1 to 5

```
С их помощью указывается количество номеров, ip-телефонов и источник, который будет раздавать ареса устройствам.


На коммутаторе были настроены vlan порты  для его взаимодействия с маршрутизатором. Для этого все порты были настроены в режиме access и через них был проброшен voice канал.

```
Switch(config)#interface range FastEthernet 0/1-4
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport voice vlan 1

```

На коммутаторе были назначены номера для IP-телефонов:

```
CMERouter(config)#ephone-dn 1
CMERouter(config-ephone-dn)#number 101
CMERouter(config-ephone-dn)#exit
CMERouter(config)#ephone-dn 2
CMERouter(config-ephone-dn)#number 102
CMERouter(config-ephone-dn)#exit
CMERouter(config)#ephone-dn 3
CMERouter(config-ephone-dn)#number 104
CMERouter(config-ephone-dn)#exit
```

Конечная конфигурация маршрутизатора :

```
CMERouter#show run
Building configuration...

Current configuration : 1239 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname CMERouter
!
!
ip dhcp pool phone
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 option 150 ip 192.168.1.1
!
!
ip cef
no ipv6 cef
!
!
license udi pid CISCO2811/K9 sn FTX1017HQ14-
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
 auto assign 1 to 5
!
ephone-dn 1
 number 101
!
ephone-dn 2
 number 102
!
ephone-dn 3
 number 103
!
ephone 1
 device-security-mode none
 mac-address 0060.7065.BAB2
 type 7960
 button 1:1
!
ephone 2
 device-security-mode none
 mac-address 0005.5E9B.B9A3
 type 7960
 button 1:2
!
ephone 3
 device-security-mode none
 mac-address 0060.3EB7.79C8
 type 7960
 button 1:3
!
line con 0
 password 12345
 login
!
line aux 0
!
line vty 0 4
 password 12345
 login
!
end
```

В итоге, была проверена связность телефонов:

![Проверка телефонов](https://github.com/LenaSpevak/2023_2024-ip-telephony-k34212-spevak_e_a/blob/main/lab2/images/call_checking1.png)

   Часть 2
   
Для второй части была в Cisco Packet Tracer была построена следующая схема:

![Схема 2](https://github.com/LenaSpevak/2023_2024-ip-telephony-k34212-spevak_e_a/blob/main/lab2/images/IP-TelephonyLab2Schema2.drawio.png)

На коммутаторе было создано два vlan: 10 - для компьютеров, между которыми передаются данные, и  20 - для ip-телефонов. Были настроены порты: fa0/1, через которое происходит подключение к маршрутизатору, настроен в режиме trunk, а fa0/2-4, ведущие к конечным устройствам, настроены в режиме access. 

```
Switch(config)#vlan 10
Switch(config-vlan)#name data
Switch(config-vlan)#exit
Switch(config)#vlan 20
Switch(config-vlan)#name voice
Switch(config-vlan)#exit
Switch(config)#int fa0/1
Switch(config-if)#switchport mode trunk
Switch(config-if)#exit
Switch(config)#interface range fa0/2-4
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport access vlan 10
Switch(config-if-range)#switchport voice vlan 20
```

Настройка маршрутизатора очень похожа на настройку в части, но для данной схемы было создно 2 dhcp pool - один для передачи данных (data), а другой для передачи голоса (voice), в котором была ещё настроена опция для возможности использования настройки CallManager Express. Для каждого влана был создан свой сабинтерфейс, а на нем настроен свой адрес из сети адресов вланов. 

```
Router(config)#ip dhcp pool data
Router(dhcp-config)#network 192.168.10.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.10.1
Router(dhcp-config)#exit
Router(config)#ip dhcp pool voice
Router(dhcp-config)#network 192.168.20.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.20.1
Router(dhcp-config)#option 150 ip 192.168.20.1
Router(dhcp-config)#exit
Router(config-if)#int fa0/0.10
Router(config-subif)#ip add 192.168.10.1 255.255.255.0
Router(config-subif)#encapsulation dot1Q 10
Router(config-subif)#ip add 192.168.10.1 255.255.255.0
Router(config-subif)#no shutdown
Router(config-subif)#exit
Router(config)#int fa0/0.20
Router(config-subif)#encapsulation dot1Q 20
Router(config-subif)#ip add 192.168.20.1 255.255.255.0
Router(config-subif)#no shutdown
Router(config-subif)#exit
```
IP адреса, которые были выданы телефонам, продемонстрированы ниже.

![Адреса телефонов](https://github.com/LenaSpevak/2023_2024-ip-telephony-k34212-spevak_e_a/blob/main/lab2/images/phones_ip-addresses.png)

Аналогично прошлой части был настроен телефонный сервер, где выделено количество номеров и устройств в сети, а также назначены номера IP-телефонам.

Итоговая конфигурация маршрутизатора представлена ниже.

```
CMERouter#show run
Building configuration...

Current configuration : 1478 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname CMERouter
!
!
!
ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp excluded-address 192.168.20.1 192.168.20.9
!
ip dhcp pool voice
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 option 150 ip 192.168.20.1
ip dhcp pool data
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
!
!
ip cef
no ipv6 cef
!
!
license udi pid CISCO2811/K9 sn FTX1017W4EZ-
!
!
no ip domain-lookup
!
!
spanning-tree mode pvst
!
!
interface FastEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
!
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
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
router rip
!
ip classless
!
ip flow-export version 9
!
!
telephony-service
 max-ephones 3
 max-dn 3
 ip source-address 192.168.20.1 port 2000
!
ephone-dn 1
 number 101
!
ephone-dn 2
 number 102
!
ephone-dn 3
 number 103
!
ephone 1
 device-security-mode none
 mac-address 00D0.BA23.4CC9
!
ephone 2
 device-security-mode none
 mac-address 0001.64DB.E785
!
ephone 3
 device-security-mode none
 mac-address 0090.0C61.85EC
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

Между компьюторами была проверена связность с помощью команды ping:

![Проверка связи компьютеров](https://github.com/LenaSpevak/2023_2024-ip-telephony-k34212-spevak_e_a/blob/main/lab2/images/ping.png)

Для проверки связи телефонов были совершены звонки.

![Проверка телефонов](https://github.com/LenaSpevak/2023_2024-ip-telephony-k34212-spevak_e_a/blob/main/lab2/images/call_checking2.png)

**Вывод** - были получены навыки построения сети IP-телефонии с помощью маршрутизатора Cisco 2811, коммутатора Cisco catalyst 3560 и IP телефонов Cisco 7960, были реализованы две схемы связи - с использованием только телефонов и с использованием компьютеров и телефонов.
