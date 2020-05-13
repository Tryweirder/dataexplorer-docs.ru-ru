---
title: ipv4_is_match () — обозреватель данных Azure
description: В этой статье описывается ipv4_is_match () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b63a73efe73223ba7c6bd2b42c6e05a6c60e94b6
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271457"
---
# <a name="ipv4_is_match"></a>ipv4_is_match ()

Совпадает с двумя строками IPv4.

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

**Синтаксис**

`ipv4_is_match(`*Выражение1* `, ` *Выражение2* `[ ,` *Префиксмаск*`])`

**Аргументы**

* *Выражение1*, *выражение2*: строковое выражение, представляющее адрес IPv4. Строки IPv4 можно маскировать с помощью [нотации префикса IP](#ip-prefix-notation).
* *Префиксмаск*: целое число от 0 до 32, представляющее количество наиболее значимых битов, которые учитываются в учетной записи.

### <a name="ip-prefix-notation"></a>IP-префиксная нотация

Обычно IP-адреса определяются с `IP-prefix notation` помощью символа косой черты ( `/` ). IP-адрес слева от косой черты ( `/` ) — это базовый IP-адрес, а число (от 1 до 32) справа от косой черты ( `/` ) — это число непрерывных 1 бит в маске. 

Пример: 192.168.2.0/24 будет иметь связанную сеть/подсети, содержащую 24 смежных бита или 255.255.255.0 в точечно-десятичном формате.

**Возвращает**

Две строки IPv4 анализируются и сравниваются при учете общей маски IP-префикса, вычисленной из префиксов аргументов, и необязательного `PrefixMask` аргумента.

Возвращает:
* `true`: Если длинное представление первого аргумента строки IPv4 равно второму аргументу строки IPv4.
*  `false`Ином.

Если преобразование одной из двух строк IPv4 не прошло успешно, результат будет таким: `null` .

**Примеры**

## <a name="ipv4-comparison-equality-cases"></a>Варианты равенства для сравнения IPv4

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
| extend result = ipv4_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|

В следующем примере сравниваются различные IP-адреса с использованием нотации префикса IP, указанной в строках IPv4, и в качестве дополнительного аргумента `ipv4_is_match()` функции.

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

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|
