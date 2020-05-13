---
title: parse_ipv4 () — обозреватель данных Azure
description: В этой статье описывается parse_ipv4 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 2bfea891b6057b5d43b65fa045e2b01d5e025a82
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271253"
---
# <a name="parse_ipv4"></a>parse_ipv4()

Преобразует строку IPv4 в длинное числовое представление (с подписью 64 бит).

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

**Синтаксис**

`parse_ipv4(`*Выражения*`)`

**Аргументы**

* *Expr*: строковое выражение, представляющее IPv4, которое будет преобразовано в Long. Строка может включать в себя NET-Mask с использованием [нотации префикса IP](#ip-prefix-notation).

### <a name="ip-prefix-notation"></a>IP-префиксная нотация

Обычно IP-адреса определяются с `IP-prefix notation` помощью символа косой черты ( `/` ).
IP-адрес слева от косой черты ( `/` ) — это базовый IP-адрес, а число (от 1 до 32) справа от косой черты (/) — это число непрерывных 1 бит в маске. Таким словами, 192.168.2.0/24 будет иметь связанную сеть/подсети, содержащую 24 смежных бита или 255.255.255.0 в точечно-десятичном формате.

**Возвращает**

Если преобразование выполнено успешно, результатом будет целое число.
Если преобразование не выполнено, результатом будет `null` .
 
**Пример**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string)
[
 '192.168.1.1',
 '192.168.1.1/24',
 '255.255.255.255/31'
]
| extend ip_long = parse_ipv4(ip_string)
```

|ip_string|ip_long|
|---|---|
|192.168.1.1|3232235777|
|192.168.1.1/24|3232235776|
|255.255.255.255/31|4294967294|
