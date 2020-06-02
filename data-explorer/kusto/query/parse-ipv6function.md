---
title: parse_ipv6 () — обозреватель данных Azure
description: В этой статье описывается функция parse_ipv6 () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: d7858a67719bbde9a1ecedee5888abc7d0662e1e
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301220"
---
# <a name="parse_ipv6"></a>parse_ipv6 ()

Преобразует строку IPv6 или IPv4 в каноническое представление строки IPv6.

```kusto
parse_ipv6("127.0.0.1") == '0000:0000:0000:0000:0000:ffff:7f00:0001'
parse_ipv6(":fe80::85d:e82c:9446:7994") == 'fe80:0000:0000:0000:085d:e82c:9446:7994'
```

**Синтаксис**

`parse_ipv6(`*`Expr`*`)`

**Аргументы**

* *`Expr`*: Строковое выражение, представляющее сетевой адрес IPv6/IPv4, который будет преобразован в каноническое представление IPv6. Строка может включать в себя NET-Mask с использованием [нотации префикса IP](#ip-prefix-notation).

## <a name="ip-prefix-notation"></a>IP-префиксная нотация

IP-адреса можно определять с `IP-prefix notation` помощью символа косой черты ( `/` ).
IP-адрес, расположенный слева от косой черты ( `/` ), является базовым IP-адресом. Число (от 1 до 127) справа от косой черты ( `/` ) — это число непрерывных 1 бит в маске сети.

**Возвращает**

Если преобразование выполнено успешно, результатом будет строка, представляющая канонический сетевой адрес IPv6.
Если преобразование не выполнено, результатом будет `null` .

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string, netmask:long)
[
 '192.168.255.255',     32,  // 32-bit netmask is used
 '192.168.255.255/24',  30,  // 24-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255',     24,  // 24-bit netmask is used
]
| extend ip_long = parse_ipv4_mask(ip_string, netmask)
```

|ip_string|сетев|ip_long|
|---|---|---|
|192.168.255.255|32|3232301055|
|192.168.255.255/24|30|3232300800|
|255.255.255.255|24|4294967040|

## <a name="next-steps"></a>Следующие шаги

Другие аналогичные функции см. в следующих статьях:

* [parse_ipv4()](parse-ipv4function.md)
* [ipv6_compare ()](ipv6-comparefunction.md)
* [ipv6_is_match ()](ipv6-is-matchfunction.md)
