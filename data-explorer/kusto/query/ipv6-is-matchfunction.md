---
title: ipv6_is_match () — обозреватель данных Azure
description: В этой статье описывается функция ipv6_is_match () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: b6d76f8ed834ec40c53321644e5cd9b7f5f93168
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347312"
---
# <a name="ipv6_is_match"></a>ipv6_is_match()

Совпадает с двумя строками адресов сети IPv6 или IPv4. Две строки IPv6/IPv4 анализируются и сравниваются при учете общей маски IP-префикса, вычисленной из префиксов аргументов, и необязательного `PrefixMask` аргумента.

```kusto
ipv6_is_match('::ffff:7f00:1', '127.0.0.1') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995') == false
ipv6_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv6_is_match('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == true
```

## <a name="syntax"></a>Синтаксис

`ipv6_is_match(`*Выражение1* `, ` *Выражение2* `[ ,` *Префиксмаск*`])`

## <a name="arguments"></a>Аргументы

* *Выражение1*, *выражение2*: строковое выражение, представляющее адрес IPv6 или IPv4. Строки IPv6 и IPv4 можно маскировать с помощью [нотации префикса IP](#ip-prefix-notation).
* *Префиксмаск*: целое число от 0 до 128, представляющее количество наиболее значимых битов, которые учитываются в учетной записи.

## <a name="ip-prefix-notation"></a>IP-префиксная нотация
 
IP-адреса можно определять с `IP-prefix notation` помощью символа косой черты ( `/` ).
IP-адрес, расположенный слева от косой черты ( `/` ), является базовым IP-адресом. Число (от 1 до 127) справа от косой черты ( `/` ) — это число непрерывных 1 бита в маске. 

## <a name="example"></a>Пример:
FE80:: 85d: e82c: 9446:7994/120 имеет связанную сетевую подсеть, содержащую 120 смежных битов.

## <a name="returns"></a>Результаты

* `true`: Если длинное представление первого аргумента строки IPv6/IPv4 равно второму строковому аргументу IPv6/IPv4.
* `false`Ином.
* `null`: Если преобразование одной из двух строк IPv6/IPv4 завершилось неудачно.

> [!Note]
> Функция может принимать и сравнивать аргументы, представляющие адреса IPv6 и сети IPv4. Если вызывающий объект знает, что аргументы имеют формат IPv4, используйте функцию [ipv4_is_match ()](./ipv4-is-matchfunction.md) . Эта функция приведет к лучшей производительности во время выполнения.

## <a name="examples"></a>Примеры

### <a name="ipv6ipv4-comparison-equality-case---ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>Регистр равенства сравнения IPv6/IPv4 — IP-префикс, указанный в строках IPv6/IPv4

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
| extend result = ipv6_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.1|192.168.1.1|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|
|FE80:: 85d: e82c: 9446:7994|FE80:: 85d: e82c: 9446:7994|1|
|FE80:: 85d: e82c: 9446:7994/120|FE80:: 85d: e82c: 9446:7998|1|
|FE80:: 85d: e82c: 9446:7994|FE80:: 85d: e82c: 9446:7998/120|1|
|FE80:: 85d: e82c: 9446:7994/120|FE80:: 85d: e82c: 9446:7998/120|1|
|192.168.1.1|:: FFFF: c0a8:0101|1|
|192.168.1.1/24|:: FFFF: c0a8:01ff|1|
|:: FFFF: c0a8:0101|192.168.1.255/24|1|
|:: 192.168.1.1/30|192.168.1.255/24|1|


### <a name="ipv6ipv4-comparison-equality-case--ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_is_match-function"></a>Регистр равенства сравнения IPv6/IPv4 — IP-префикс, указанный в строках IPv6/IPv4, и в качестве дополнительного аргумента `ipv6_is_match()` функции

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
| extend result = ipv6_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|
|FE80:: 85d: e82c: 9446:7994|FE80:: 85d: e82c: 9446:7995|127|1|
|FE80:: 85d: e82c: 9446:7994/127|FE80:: 85d: e82c: 9446:7998|120|1|
|FE80:: 85d: e82c: 9446:7994/120|FE80:: 85d: e82c: 9446:7998|127|1|
|192.168.1.1/24|:: FFFF: c0a8:01ff|127|1|
|:: FFFF: c0a8:0101|192.168.1.255|120|1|
|:: 192.168.1.1/30|192.168.1.255/24|127|1|
