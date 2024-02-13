# Отчет по лабораторной работе №1 "Базовая настройка ip-телефонов в среде Сisco packet tracer"
University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [IP telephony technologies](https://itmo-ict-faculty.github.io/ip-telephony/)

Year: 2023/2024

Group: K34212

Author: Kostenko Darina Alekseevna

Lab: Lab1

Date of create: 09.02.2024

Date of finished: 13.02.2024

## Цель работы: 

Изучить рабочую среду Cisco Packet Tracer, ознакомиться с интерфейсами основных устройств, типами кабелей, научиться собирать топологию. Изучить построение сети IP-телефонии с помощью маршрутизатора, коммутатора и IP телефонов Cisco 7960 в среде Packet tracer.

## Ход работы:

- ### Часть 1

В среде Cisco Packet Tracer была собрана схема соединения:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab1/lab1_pics/diagram1.png)

Интерфейсу каждого PC был задан ip-адрес 192.168.0.10-192.168.0.17/24 соответственно. Пример настройки на PC0:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab1/lab1_pics/pc_config.png)

Посредством пинга убедимся, что любой компьютер одной сети передает пакеты любому компьютеру другой сети.

Пинг с PC0:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab1/lab1_pics/pc0_ping.png)

Пинг с PC6:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab1/lab1_pics/pc6_ping.png)

- ### Часть 2

В среде Cisco Packet Tracer была собрана схема соединения, имя маршрутизатора было изменено на CMERouter:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab1/lab1_pics/diagram2.png)

Для работы IP-телефоны в разделе физического предcтавления подключим к источнику питания:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab1/lab1_pics/ip_phone_powerup.png)

На маршрутизаторе поднимем интерфейс FastEthernet 0/0 и присвоим ему IP–адрес для работы сети:

```
Router>en
Router#conf t
Router(config)#interface FastEthernet 0/0
Router(config-if)#ip address 192.168.0.1 255.255.255.0
Router(config-if)#no shutdown
```

Также на маршрутизаторе настроим  DHCP-сервер, выделим пул для IP–телефонов:

```
Router(config)#ip dhcp pool voice
Router(dhcp-config)#network 192.168.0.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.0.1
Router(dhcp-config)#option 150 ip 192.168.0.1
```

Далее на маршрутизаторе настроим VoIP параметры (максимально возможное количество поддерживаемых номеров и телефонов, присвоение линий в автоматическом режиме):

```
Router(config)#telephony-service
Router(config-telephony)#max-dn 15
Router(config-telephony)#max-ephones 15
Router(config-telephony)#ip source-address 192.168.0.1 port 3100
Router(config-telephony)#auto assign 1 to 19
```

На коммутаторе на подключенных интерфейсах включим поддержку VoIP (голосовой VLAN):

```
Switch>en
Switch#conf t
Switch(config)#interface FastEthernet 0/1
Switch(config-if)#switchport mode access
Switch(config-if)#switchport voice vlan 1
Switch(config-if)#interface range FastEthernet 0/23-24
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport voice vlan 1
```

На маршрутизаторе присваиваем телефонам номера:

```
Router(config)#ephone-dn 1
Router(config-ephone-dn)#number 11111
Router(config-ephone-dn)#exit
Router(config)#ephone-dn 2
Router(config-ephone-dn)#number 22222
```

На рисунке можно увидеть, что IP Phone0 получил IP-адрес 192.168.0.2/24 и номер 11111:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab1/lab1_pics/ip_phone0_check.png)

Проверим звонок между телефонами:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab1/lab1_pics/ip_phones_check.png)


## Вывод:

При выполнении работы в среде Cisco Packet Tracer были построены две топологии сети. Были изучены базовый функционал Cisco Packet Tracer, настройка сети IP-телефонии.
