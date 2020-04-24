---
title: ipv4_compare () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны ipv4_compare() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: ddaaa4e1f9dcf9dfbcd55406cd26e8f1ff4a02fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513691"
---
# <a name="ipv4_compare"></a>ipv4_compare()

Сравнивает две строки IPv4.

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

**Синтаксис**

`ipv4_compare(`*Expr1*`, `*Expr2*`[ ,`*ПрефиксМаск*`])`

**Аргументы**

* *Expr1*, *Expr2*: Выражение строки, представляющее адрес IPv4. Строки IPv4 могут быть замаскированы с помощью [обозначения IP-префикса.](#ip-prefix-notation)
* *PrefixMask*: Целый ряд от 0 до 32 представляющих количество наиболее значительных битов, которые принимаются во внимание.

### <a name="ip-prefix-notation"></a>Нотация IP-префикса

Это обычная практика для определения `IP-prefix notation` IP-адресов с использованием слэйс ( )`/`характер.
IP-адрес на LEFT слэша ()`/`является базовым IP-адресом, и число (1`/`до 32) к ПРАВКЕ слэша () является число смежных 1 бит в netmask. 

Пример: 192.168.2.0/24 будет иметь связанную сеть/поднетную маску, содержащую 24 смежных бита или 255.255.0 в пунктирном десятичном формате.

**Возвращает**

Две строки IPv4 разбираются и сравниваются при учете комбинированной маски IP-префикса, рассчитанной на основе префиксов аргумента, и факультативного `PrefixMask` аргумента.

Возвращает:
* `0`: Если длинное представление первого аргумента строки IPv4 равно второму аргументу строки IPv4
* `1`: Если длинное представление первого аргумента строки IPv4 больше, чем второй аргумент строки IPv4
* `-1`: Если длинное представление первого аргумента строки IPv4 меньше, чем второй аргумент строки IPv4

Если конверсия для одной из двух строк IPv4 `null`не была успешной, результат будет .

## <a name="examples-ipv4-comparison-equality-cases"></a>Примеры: случаи сопоставления IPv4

Следующий пример сравнивает различные IP-адреса с использованием IP-префикса, указанного внутри строк IPv4.

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

|ip1_string|ip2_string|набор по|
|---|---|---|
|192.168.1.0|192.168.1.0|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|

Следующий пример сравнивает различные IP-адреса, используя IP-префиксное обозначение, `ipv4_compare()` указанное внутри строк IPv4, и в качестве дополнительного аргумента функции.

```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|набор по|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|