---
title: ipv4_is_match () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны ipv4_is_match () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: aa0646321af2d467c1e4af07fba81ccdc58eff37
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513742"
---
# <a name="ipv4_is_match"></a>ipv4_is_match()

Соответствует двум строкам IPv4.

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

**Синтаксис**

`ipv4_is_match(`*Expr1*`, `*Expr2*`[ ,`*ПрефиксМаск*`])`

**Аргументы**

* *Expr1*, *Expr2*: Выражение строки, представляющее адрес IPv4. Строки IPv4 могут быть замаскированы с помощью [обозначения IP-префикса.](#ip-prefix-notation)
* *PrefixMask*: Целый ряд от 0 до 32 представляющих количество наиболее значительных битов, которые принимаются во внимание.

### <a name="ip-prefix-notation"></a>Нотация IP-префикса

Это обычная практика для определения `IP-prefix notation` IP-адресов с использованием слэйс ( )`/`характер. IP-адрес на LEFT слэша ()`/`является базовым IP-адресом, и число (1`/`до 32) к ПРАВКЕ слэша () является число смежных 1 бит в netmask. 

Пример: 192.168.2.0/24 будет иметь связанную сеть/поднетную маску, содержащую 24 смежных бита или 255.255.0 в пунктирном десятичном формате.

**Возвращает**

Две строки IPv4 разбираются и сравниваются при учете комбинированной маски IP-префикса, рассчитанной на основе префиксов аргумента, и факультативного `PrefixMask` аргумента.

Возвращает:
* `true`: Если длинное представление первого аргумента строки IPv4 равно второму аргументу строки IPv4.
*  `false`: В противном случае.

Если конверсия для одной из двух строк IPv4 `null`не была успешной, результат будет .

**Примеры**

## <a name="ipv4-comparison-equality-cases"></a>Случаи сравнения IPv4

Следующий пример сравнивает различные IP-адреса с использованием IP-префикса, указанного внутри строк IPv4.

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

Следующий пример сравнивает различные IP-адреса, используя IP-префиксное обозначение, `ipv4_is_match()` указанное внутри строк IPv4, и в качестве дополнительного аргумента функции.

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