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
ms.openlocfilehash: 9fec1869ee06e4fd9a9932e42c6ab1049b50a04f
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271508"
---
# <a name="ipv4_compare"></a>ipv4_compare ()

Сравнивает две строки IPv4.

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

**Синтаксис**

`ipv4_compare(`*Выражение1* `, ` *Выражение2* `[ ,` *Префиксмаск*`])`

**Аргументы**

* *Выражение1*, *выражение2*: строковое выражение, представляющее адрес IPv4. Строки IPv4 можно маскировать с помощью [нотации префикса IP](#ip-prefix-notation).
* *Префиксмаск*: целое число от 0 до 32, представляющее количество наиболее значимых битов, которые учитываются в учетной записи.

### <a name="ip-prefix-notation"></a>IP-префиксная нотация

Обычно IP-адреса определяются с `IP-prefix notation` помощью символа косой черты ( `/` ).
IP-адрес слева от косой черты ( `/` ) — это базовый IP-адрес, а число (от 1 до 32) справа от косой черты ( `/` ) — это число непрерывных 1 бит в маске. 

Пример: 192.168.2.0/24 будет иметь связанную сеть/подсети, содержащую 24 смежных бита или 255.255.255.0 в точечно-десятичном формате.

**Возвращает**

Две строки IPv4 анализируются и сравниваются при учете общей маски IP-префикса, вычисленной из префиксов аргументов, и необязательного `PrefixMask` аргумента.

Возвращает:
* `0`: Если длинное представление первого аргумента строки IPv4 равно второму аргументу строки IPv4
* `1`: Если длинное представление первого аргумента строки IPv4 больше, чем второй аргумент строки IPv4
* `-1`: Если длинное представление первого аргумента строки IPv4 меньше, чем второй аргумент строки IPv4

Если преобразование одной из двух строк IPv4 не прошло успешно, результат будет таким: `null` .

## <a name="examples-ipv4-comparison-equality-cases"></a>Примеры: сравнение IPv4-вариантов равенства

В следующем примере сравниваются различные IP-адреса с использованием нотации префикса IP, указанной в строках IPv4.

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

В следующем примере сравниваются различные IP-адреса с использованием нотации префикса IP, указанной в строках IPv4, и в качестве дополнительного аргумента `ipv4_compare()` функции.

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
