# Отчет по лабораторной работе №3 "Использование Asterisk в качестве SIP proxy"
University: ITMO University (https://itmo.ru/ru/)

Faculty: FICT (https://fict.itmo.ru/)

Course: IP telephony technologies (https://itmo-ict-faculty.github.io/ip-telephony/)

Year: 2023/2024

Group: K34212

Author: Kostenko Darina Alekseevna

Lab: Lab3

Date of create: 22.02.2024

Date of finished: 

## Цель работы: 

Изучить программный комплекс Asterisk, настроить Asterisk для локальных звонков.

## Ход работы:

### Установка и настройка Asterisk

Устанавливаем Asterisk на Ubuntu:

```
sudo apt install asterisk
```

Для добавления двух SIP-аккаунтов (1001 и 1002) и маршрутизации внутренних вызовов в файл /etc/asterisk/sip.conf было добавлено:

```
[1001]
type=friend
host=dynamic
secret=111
context=ext_1001

[1002]
type=friend
host=dynamic
secret=222
context=ext_1002

[internal_calls]
exten => 1001,1,Dial(SIP/1002)
exten => 1002,1,Dial(SIP/1001)

[default]
include => internal_calls
```

Также была добавлена информация в файл /etc/asterisk/extensions.conf:

```
[ext_1001]
exten => _XXXX,1,Dial(SIP/${EXTEN})

[ext_1002]
exten => _XXXX,1,Dial(SIP/${EXTEN})
```

Для применения настроек Asterisk был перезагружен:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab3/lab3_pics/asterisk_status.jpeg)

Открываем терминал Asterisk и проверяем добавление аккаунтов:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab3/lab3_pics/sip_peers0.png)

### Установка Zoiper

Далее в качестве soft-телефона с [официального сайта](https://www.zoiper.com/en/voip-softphone/download/current) был установлен Zoiper5. При входе были введены данные аккаунта 1001:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab3/lab3_pics/zoiper.png)

Далее в качестве сервера был указан локальный хост (127.0.0.1).

Аккаунт 1001 получил свой порт:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab3/lab3_pics/sip_peers1.png)

### Установка MicroSIP

В качестве второго soft-телефона был установлен MicroSIP (с помощью предварительно установленного wine). Здесь были введены данные аккаунта 1002, в качестве сервера так же был указан локальный хост (127.0.0.1).:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab3/lab3_pics/microsip.png)

Аккаунт 1002 также получил свой порт:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab3/lab3_pics/sip_peers2.png)

### Звонок

Далее с 1002 был совершен звонок на 1001:

![](https://github.com/kostenkoda/2023_2024-ip-telephony-k34212-kostenko_d_a/blob/main/lab3/lab3_pics/call.png)

Разговор идет, связь есть.

## Вывод:

При выполнении работы был изучен и настроен для локальных звонков программный комплекс Asterisk, был проведен звонок между двумя SIP-аккаунтами с помощью soft-телефонов Zoiper и MicroSIP.
