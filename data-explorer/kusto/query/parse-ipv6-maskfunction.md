---
title: parse_ipv6_mask () — обозреватель данных Azure
description: В этой статье описывается функция parse_ipv6_mask () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b917d592a00bb2bacd940b4dc943186e10d1bf8c
ms.sourcegitcommit: 5e903c61e779f7bf62f745f13a6038ce2a32e934
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88512552"
---
# <a name="parse_ipv6_mask"></a>parse_ipv6_mask()
 
Преобразует строку IPv6/IPv4 и маску сети в каноническое представление строки IPv6.

```kusto
parse_ipv6_mask("127.0.0.1", 24) == '0000:0000:0000:0000:0000:ffff:7f00:0000'
parse_ipv6_mask(":fe80::85d:e82c:9446:7994", 120) == 'fe80:0000:0000:0000:085d:e82c:9446:7900'
```

## <a name="syntax"></a>Синтаксис

`parse_ipv6_mask(`*`Expr`*`, `*`PrefixMask`*`)`

## <a name="arguments"></a>Аргументы

* *`Expr`*: Строковое выражение, представляющее сетевой адрес IPv6/IPv4, который будет преобразован в каноническое представление IPv6. Строка может включать в себя NET-Mask с использованием [нотации префикса IP](#ip-prefix-notation).
* *`PrefixMask`*: Целое число от 0 до 128, представляющее количество наиболее значимых битов, которые учитываются в учетной записи.

## <a name="ip-prefix-notation"></a>IP-префиксная нотация

IP-адреса можно определять с `IP-prefix notation` помощью символа косой черты ( `/` ).
IP-адрес, расположенный слева от косой черты ( `/` ), является базовым IP-адресом. Число (от 1 до 127) справа от косой черты ( `/` ) — это число непрерывных 1 бита в маске.

## <a name="returns"></a>Возвращаемое значение

Если преобразование выполнено успешно, результатом будет строка, представляющая канонический сетевой адрес IPv6.
Если преобразование не выполнено, результатом будет `null` .

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string, netmask:long)
[
 // IPv4 addresses
 '192.168.255.255',     120,  // 120-bit netmask is used
 '192.168.255.255/24',  124,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255', 128,  // 128-bit netmask is used
 // IPv6 addresses
 'fe80::85d:e82c:9446:7994', 128,     // 128-bit netmask is used
 'fe80::85d:e82c:9446:7994/120', 124, // 120-bit netmask is used
 // IPv6 with IPv4 notation
 '::192.168.255.255',    128,  // 128-bit netmask is used
 '::192.168.255.255/24', 128,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
]
| extend ip6_canonical = parse_ipv6_mask(ip_string, netmask)
```

|ip_string|сетев|ip6_canonical|
|---|---|---|
|192.168.255.255|120|0000:0000:0000:0000:0000: FFFF: c0a8: FF00|
|192.168.255.255/24|124|0000:0000:0000:0000:0000: FFFF: c0a8: FF00|
|255.255.255.255|128|0000:0000:0000:0000:0000: FFFF: FFFF: FFFF|
|FE80:: 85d: e82c: 9446:7994|128|FE80:0000:0000:0000:085d: e82c: 9446:7994|
|FE80:: 85d: e82c: 9446:7994/120|124|FE80:0000:0000:0000:085d: e82c: 9446:7900|
|:: 192.168.255.255|128|0000:0000:0000:0000:0000: FFFF: c0a8: FFFF|
|:: 192.168.255.255/24|128|0000:0000:0000:0000:0000: FFFF: c0a8: FF00|

