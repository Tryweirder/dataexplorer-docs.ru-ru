---
title: ipv4_is_private () — обозреватель данных Azure
description: В этой статье описывается функция ipv4_is_private () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/20/2020
ms.openlocfilehash: 161e0a2ca94bb9b037fc619c0fc5dfbddd8d28da
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324945"
---
# <a name="ipv4_is_private"></a>ipv4_is_private ()

Проверяет, принадлежит ли строковый адрес IPv4 набору IP-адресов частной сети.

[Адреса частной сети](https://en.wikipedia.org/wiki/Private_network) были изначально определены, чтобы помочь в задержке исчерпания IPv4-адресов. IP-пакеты, отправляемые или адресованные частному IP-адресу, не могут маршрутизироваться через общедоступный Интернет.

## <a name="private-ipv4-addresses"></a>Частные IPv4-адреса

В стандарте Internet Engineering Task Force (IETF) для резервирования следующих диапазонов IPv4-адресов для частных сетей направляется служба Assigned Numbers Authority (IANA).

| Диапазон IP-адресов|Число адресов|Самый крупный блок CIDR (маска подсети)|
|-----------------|-------------------|--------------------------------|
|10.0.0.0–10.255.255.255;|16777216|10.0.0.0/8 (255.0.0.0)|
|172.16.0.0–172.31.255.255;|1048576|172.16.0.0/12 (255.240.0.0)|
|192.168.0.0–192.168.255.255.|65536|192.168.0.0/16 (255.255.0.0)|


```kusto
ipv4_is_private('192.168.1.1/24') == true
ipv4_is_private('10.1.2.3/24') == true
ipv4_is_private('202.1.2.3') == false
ipv4_is_private("127.0.0.1") == false
```

## <a name="syntax"></a>Синтаксис

`ipv4_is_private(`*Выражения*`)`

## <a name="arguments"></a>Аргументы

*Expr*: строковое выражение, представляющее адрес IPv4. Строки IPv4 можно маскировать с помощью [нотации префикса IP](#ip-prefix-notation).

### <a name="ip-prefix-notation"></a>IP-префиксная нотация

IP-адреса можно определять с `IP-prefix notation` помощью символа косой черты ( `/` ). IP-адрес, расположенный слева от косой черты ( `/` ), является базовым IP-адресом. Число (от 1 до 32) справа от косой черты ( `/` ) — это число непрерывных 1 бита в маске. 

Например, в 192.168.2.0/24 будет сопоставлена сеть/маска подсети, содержащая 24 смежных бита или 255.255.255.0 в точечно-десятичном формате.

## <a name="returns"></a>Возвращаемое значение

* `true`: Если IPv4-адрес принадлежит какому-либо из диапазонов частной сети.
*  `false`Ином.
* `null`: Если преобразование одной из двух строк IPv4 завершилось неудачно.

## <a name="example-check-if-ipv4-belongs-to-a-private-network"></a>Пример. Проверьте, относится ли IPv4 к частной сети.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string)
[
 '10.1.2.3',
 '192.168.1.1/24',
 '127.0.0.1',
]
| extend result = ipv4_is_private(ip_string)
```

|ip_string|набор по|
|---|---|
|10.1.2.3|1|
|192.168.1.1/24|1|
|127.0.0.1|0|
