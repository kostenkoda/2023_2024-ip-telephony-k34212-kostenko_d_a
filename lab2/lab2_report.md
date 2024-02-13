
# Отчет по лабораторной работе №2 "Конфигурация voip в среде Сisco packet tracer"
University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [IP telephony technologies](https://itmo-ict-faculty.github.io/ip-telephony/)

Year: 2023/2024

Group: K34212

Author: Kostenko Darina Alekseevna

Lab: Lab2

Date of create: 09.02.2024

Date of finished: 13.02.2024

## Цель работы: 

Изучить построение сети IP-телефонии с помощью маршрутизатора Cisco 2811, коммутатора Cisco catalyst 3560 и IP телефонов Cisco 7960.

## Ход работы:

### Часть 1

В среде Cisco Packet Tracer была собрана схема соединения:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab2/lab2_pics/diagram1.png)

В конфигурационном режиме название маршрутизатора было изменено на CMERouter:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab2/lab2_pics/router_config.png)

Для работы IP-телефоны в разделе физического предcтавления подключим к источнику питания:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab2/lab2_pics/ip_phone_powerup.png)

На маршрутизаторе был отключен синтаксис ввода слов от DNS серверов:

```
CMERouter>en
CMERouter#conf t
CMERouter(config)#no ip domain-lookup
```

Также были заданы пароли для защиты маршрутизатора в удаленном режиме и в режиме консоли:

```
CMERouter(config)#line vty 0 4
CMERouter(config-line)#password pswd1234
CMERouter(config-line)#login
CMERouter(config-line)#exit
CMERouter(config)#line console 0
CMERouter(config-line)#password pswd5678
CMERouter(config-line)#login
```

На маршрутизаторе поднимем интерфейс FastEthernet 0/0 и присвоим ему IP–адрес:

```
CMERouter(config)#interface FastEthernet 0/0
CMERouter(config-if)#ip address 192.168.0.1 255.255.255.0
CMERouter(config-if)#no shutdown
```

Также на маршрутизаторе настроим DHCP-сервер, выделим пул для IP–телефонов:

```
CMERouter(config)#ip dhcp pool voice
CMERouter(dhcp-config)#network 192.168.0.0 255.255.255.0
CMERouter(dhcp-config)#default-router 192.168.0.1
CMERouter(dhcp-config)#option 150 ip 192.168.0.1
```

Далее на маршрутизаторе настроим VoIP параметры (максимально возможное количество поддерживаемых номеров и телефонов, присвоение линий в автоматическом режиме):

```
CMERouter(config)#telephony-service
CMERouter(config-telephony)#max-dn 15
CMERouter(config-telephony)#max-ephones 15
CMERouter(config-telephony)#ip source-address 192.168.0.1 port 3100
CMERouter(config-telephony)#auto assign 1 to 15
```

На коммутаторе на подключенных интерфейсах включаем поддержку VoIP (голосовой VLAN):

```
Switch>en
Switch#conf t
Switch(config)#interface FastEthernet 0/1
Switch(config-if)#switchport mode access
Switch(config-if)#switchport voice vlan 1
Switch(config-if)#interface range FastEthernet 0/22-24
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport voice vlan 1
```

На маршрутизаторе присваиваем телефонам номера:

```
CMERouter(config)#ephone-dn 1
CMERouter(config-ephone-dn)#number 1111
CMERouter(config-ephone-dn)#exit
CMERouter(config)#ephone-dn 2
CMERouter(config-ephone-dn)#number 2222
CMERouter(config-ephone-dn)#exit
CMERouter(config)#ephone-dn 3
CMERouter(config-ephone-dn)#number 3333
```

Проверим звонок между телефонами:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab2/lab2_pics/ip_phone_check1.png)

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab2/lab2_pics/ip_phone_check2.png)

### Часть 2

В среде Cisco Packet Tracer была собрана схема соединения:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab2/lab2_pics/diagram2.png)

Для работы IP-телефоны были подключены к источнику питания в разделе физического предcтавления.

На коммутаторе были добавлены 2 VLAN для передачи данных и голоса:

```
Switch(config)#vlan 10
Switch(config-vlan)#name data
Switch(config-vlan)#vlan 20
Switch(config-vlan)#name voice
```

Интерфейс коммутатора, подключенный к роутеру, был переведен в режим trunk, была разрешена передача добавленных VLAN:

```
Switch(config)#interface FastEthernet 0/1
Switch(config-if)#switchport mode trunk
Switch(config-if)#switchport trunk allowed vlan 1,10,20
```

Интерфейсы коммутатора, подключенные к ip-телефонам, были переведены в режим access для vlan 10 и vlan 20 в качестве голосового:

```
Switch(config)#interface range FastEthernet 0/22-24
Switch(config-if-range)#switchport access vlan 10
Switch(config-if-range)#switchport voice vlan 20
```

На маршрутизаторе были подняты саб-интерфейсы, соответсвующие vlan, им были назначены IP–адреса:

```
Router(config-subif)#interface FastEthernet0/0
Router(config-subif)#no shutdown
Router(config-subif)#interface FastEthernet0/0.10
Router(config-subif)#encapsulation dot1Q 10
Router(config-subif)#ip address 192.168.10.1 255.255.255.0
Router(config-subif)#interface FastEthernet0/0.20
Router(config-subif)#encapsulation dot1Q 20
Router(config-subif)#ip address 192.168.20.1 255.255.255.0
```

Также на маршрутизаторе настроим DHCP-сервер, выделим пулы для ПК и IP–телефонов:

```
Router(config)#ip dhcp pool data
Router(dhcp-config)#network 192.168.10.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.10.1
Router(dhcp-config)#ip dhcp pool voice
Router(dhcp-config)#network 192.168.20.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.20.1
Router(dhcp-config)#option 150 ip 192.168.20.1
```

На компьютерах включим получение ip-адреса по DHCP:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab2/lab2_pics/pc_config.png)

Далее на маршрутизаторе настроим VoIP параметры:

```
Router(config)#telephony-service
Router(config-telephony)#max-dn 15
Router(config-telephony)#max-ephones 15
Router(config-telephony)#ip source-address 192.168.20.1 port 3100
Router(config-telephony)#auto assign 1 to 15
```

Присвоим телефонам номера:

```
CMERouter(config)#ephone-dn 1
CMERouter(config-ephone-dn)#number 1111
CMERouter(config-ephone-dn)#exit
CMERouter(config)#ephone-dn 2
CMERouter(config-ephone-dn)#number 2222
CMERouter(config-ephone-dn)#exit
CMERouter(config)#ephone-dn 3
CMERouter(config-ephone-dn)#number 3333
```

Проверим звонок между телефонами:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab2/lab2_pics/ip_phone_check3.png)

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab2/lab2_pics/ip_phone_check4.png)

Также проверим связь между компьютерами:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab2/lab2_pics/pc_check.png)

## Вывод:

При выполнении работы в среде Cisco Packet Tracer были построены две топологии сети. Была получены навыки по построению сетей IP-телефонии.
