---
title: ipv4_compare () — обозреватель данных Azure
description: В этой статье описывается ipv4_compare () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 68082b68a1c7772135f711248ddfcd4079bc753e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803936"
---
# <a name="ipv4_compare"></a>ipv4_compare()

Сравнивает две строки IPv4. Две строки IPv4 анализируются и сравниваются при учете общей маски IP-префикса, вычисленной из префиксов аргументов, и необязательного `PrefixMask` аргумента.

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

## <a name="syntax"></a>Синтаксис

`ipv4_compare(`*Выражение1* `, ` *Выражение2* `[ ,` *Префиксмаск*`])`

## <a name="arguments"></a>Аргументы

* *Выражение1*, *выражение2*: строковое выражение, представляющее адрес IPv4. Строки IPv4 можно маскировать с помощью [нотации префикса IP](#ip-prefix-notation).
* *Префиксмаск*: целое число от 0 до 32, представляющее количество наиболее значимых битов, которые учитываются в учетной записи.

## <a name="ip-prefix-notation"></a>IP-префиксная нотация
 
IP-адреса можно определять с `IP-prefix notation` помощью символа косой черты ( `/` ).
IP-адрес, расположенный слева от косой черты ( `/` ), является базовым IP-адресом. Число (от 1 до 32) справа от косой черты ( `/` ) — это число непрерывных 1 бита в маске. 

Например, в 192.168.2.0/24 будет сопоставлена сеть/маска подсети, содержащая 24 смежных бита или 255.255.255.0 в точечно-десятичном формате.

## <a name="returns"></a>Возвращаемое значение

* `0`: Если длинное представление первого аргумента строки IPv4 равно второму аргументу строки IPv4
* `1`: Если длинное представление первого аргумента строки IPv4 больше, чем второй аргумент строки IPv4
* `-1`: Если длинное представление первого аргумента строки IPv4 меньше, чем второй аргумент строки IPv4
* `null`: Если преобразование одной из двух строк IPv4 завершилось неудачно.

## <a name="examples-ipv4-comparison-equality-cases"></a>Примеры: сравнение IPv4-вариантов равенства

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv4-strings"></a>Сравнение IP-адресов с помощью нотации префикса IP, указанной в строках IPv4

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv4-strings-and-as-additional-argument-of-the-ipv4_compare-function"></a>Сравните IP-адреса с помощью нотации префикса IP, указанной в строках IPv4, и в качестве дополнительного аргумента `ipv4_compare()` функции.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|

