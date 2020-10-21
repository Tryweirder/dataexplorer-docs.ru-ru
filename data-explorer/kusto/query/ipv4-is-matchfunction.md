---
title: ipv4_is_match () — обозреватель данных Azure
description: В этой статье описывается ipv4_is_match () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 0e00b2bffb31f66fc0e684bb5300af2c334fc825
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242463"
---
# <a name="ipv4_is_match"></a>ipv4_is_match()

Совпадает с двумя строками IPv4. Две строки IPv4 анализируются и сравниваются при учете общей маски IP-префикса, вычисленной из префиксов аргументов, и необязательного `PrefixMask` аргумента.

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

## <a name="syntax"></a>Синтаксис

`ipv4_is_match(`*Выражение1* `, ` *Выражение2* `[ ,` *Префиксмаск*`])`

## <a name="arguments"></a>Аргументы

* *Выражение1*, *выражение2*: строковое выражение, представляющее адрес IPv4. Строки IPv4 можно маскировать с помощью [нотации префикса IP](#ip-prefix-notation).
* *Префиксмаск*: целое число от 0 до 32, представляющее количество наиболее значимых битов, которые учитываются в учетной записи.

## <a name="ip-prefix-notation"></a>IP-префиксная нотация

IP-адреса можно определять с `IP-prefix notation` помощью символа косой черты ( `/` ). IP-адрес, расположенный слева от косой черты ( `/` ), является базовым IP-адресом. Число (от 1 до 32) справа от косой черты ( `/` ) — это число непрерывных 1 бита в маске. 

Например, в 192.168.2.0/24 будет сопоставлена сеть/маска подсети, содержащая 24 смежных бита или 255.255.255.0 в точечно-десятичном формате.

## <a name="returns"></a>Результаты

* `true`: Если длинное представление первого аргумента строки IPv4 равно второму аргументу строки IPv4.
*  `false`Ином.
* `null`: Если преобразование одной из двух строк IPv4 завершилось неудачно.

## <a name="examples"></a>Примеры

### <a name="ipv4-comparison-equality---ip-prefix-notation-specified-inside-the-ipv4-strings"></a>Равенство сравнения IPv4-IP-префиксная нотация, указанная в строках IPv4

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|набор по|
|---|---|---|
|192.168.1.0|192.168.1.0|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|

### <a name="ipv4-comparison-equality---ip-prefix-notation-specified-inside-the-ipv4-strings-and-an-additional-argument-of-the-ipv4_is_match-function"></a>Равенство сравнения IPv4-IP-префикс, указанный в строках IPv4, и дополнительный аргумент `ipv4_is_match()` функции

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|набор по|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|
