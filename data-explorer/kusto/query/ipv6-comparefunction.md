---
title: ipv6_compare () — обозреватель данных Azure
description: В этой статье описывается функция ipv6_compare () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: ce14c466d927dd2431ae8e057bceec0d5fdd38b8
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803902"
---
# <a name="ipv6_compare"></a>ipv6_compare()

Сравнивает две строки адресов сети IPv6 или IPv4. Две строки IPv6 анализируются и сравниваются при учете общей маски IP-префикса, вычисленной из префиксов аргументов, и необязательного `PrefixMask` аргумента.

```kusto
ipv6_compare('::ffff:7f00:1', '127.0.0.1') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995')  < 0
ipv6_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv6_compare('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == 0
```

> [!Note]
> Функция может принимать и сравнивать аргументы, представляющие адреса IPv6 и сети IPv4. Однако если вызывающий объект знает, что аргументы имеют формат IPv4, используйте функцию [ipv4_is_compare ()](./ipv4-comparefunction.md) . Эта функция приведет к лучшей производительности во время выполнения.

## <a name="syntax"></a>Синтаксис

`ipv6_compare(`*Выражение1* `, ` *Выражение2* `[ ,` *Префиксмаск*`])`

## <a name="arguments"></a>Аргументы

* *Выражение1*, *выражение2*: строковое выражение, представляющее адрес IPv6 или IPv4. Строки IPv6 и IPv4 можно маскировать с помощью нотации префикса IP (см. Примечание).
* *Префиксмаск*: целое число от 0 до 128, представляющее количество наиболее значимых битов, которые учитываются в учетной записи.

## <a name="ip-prefix-notation"></a>IP-префиксная нотация

Обычно IP-адреса определяются с `IP-prefix notation` помощью символа косой черты ( `/` ).
IP-адрес слева от косой черты ( `/` ) — это базовый IP-адрес, а число (от 1 до 127) справа от косой черты ( `/` ) — это число непрерывных 1 бит в маске. 

Например, FE80:: 85d: e82c: 9446:7994/120 будет иметь связанную сетевую или подсеть, содержащую 120 смежных битов.

## <a name="returns"></a>Возвращаемое значение

* `0`: Если длинное представление первого аргумента строки IPv6 равно второму аргументу строки IPv6.
* `1`: Если длинное представление первого строкового аргумента IPv6 больше, чем второй аргумент строки IPv6.
* `-1`: Если длинное представление первого строкового аргумента IPv6 меньше, чем второй аргумент строки IPv6.
* `null`: Если преобразование одной из двух строк IPv6 завершилось неудачно.

## <a name="examples-ipv6ipv4-comparison-equality-cases"></a>Примеры: Сравнение вариантов равенства для IPv6/IPv4

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>Сравнение IP-адресов с помощью нотации префикса IP, указанной в строках IPv6/IPv4

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 // IPv4 are compared as IPv6 addresses
 '192.168.1.1',    '192.168.1.1',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
  // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7994',         // Equal IPs
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7998/120',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998/120', // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1',      '::ffff:c0a8:0101', // Equal IPs
 '192.168.1.1/24',   '::ffff:c0a8:01ff', // 24 bit IP-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
]
| extend result = ipv6_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.1|192.168.1.1|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|
|FE80:: 85d: e82c: 9446:7994|FE80:: 85d: e82c: 9446:7994|0|
|FE80:: 85d: e82c: 9446:7994/120|FE80:: 85d: e82c: 9446:7998|0|
|FE80:: 85d: e82c: 9446:7994|FE80:: 85d: e82c: 9446:7998/120|0|
|FE80:: 85d: e82c: 9446:7994/120|FE80:: 85d: e82c: 9446:7998/120|0|
|192.168.1.1|:: FFFF: c0a8:0101|0|
|192.168.1.1/24|:: FFFF: c0a8:01ff|0|
|:: FFFF: c0a8:0101|192.168.1.255/24|0|
|:: 192.168.1.1/30|192.168.1.255/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_compare-function"></a>Сравните IP-адреса с помощью нотации префикса IP, указанной в строках IPv6/IPv4, а также в качестве дополнительного аргумента `ipv6_compare()` функции.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 // IPv4 are compared as IPv6 addresses 
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP4-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP4-prefix is used for comparison
   // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995',     127, // 127 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7998', 120, // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998', 127, // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1/24',   '::ffff:c0a8:01ff', 127, // 127 bit IP6-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255',    120, // 120 bit IP6-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', 127, // 120 bit IP6-prefix is used for comparison
]
| extend result = ipv6_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|
|FE80:: 85d: e82c: 9446:7994|FE80:: 85d: e82c: 9446:7995|127|0|
|FE80:: 85d: e82c: 9446:7994/127|FE80:: 85d: e82c: 9446:7998|120|0|
|FE80:: 85d: e82c: 9446:7994/120|FE80:: 85d: e82c: 9446:7998|127|0|
|192.168.1.1/24|:: FFFF: c0a8:01ff|127|0|
|:: FFFF: c0a8:0101|192.168.1.255|120|0|
|:: 192.168.1.1/30|192.168.1.255/24|127|0|

