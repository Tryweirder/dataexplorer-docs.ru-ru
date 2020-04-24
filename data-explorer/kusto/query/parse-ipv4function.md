---
title: parse_ipv4 () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны parse_ipv4 () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 296c7e77a2397501ae086e16154ca249249c23e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511753"
---
# <a name="parse_ipv4"></a>parse_ipv4()

Преобразует строку IPv4 в длинное (подписанное 64-разрядное) представление чиномер.

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

**Синтаксис**

`parse_ipv4(`*Expr*`)`

**Аргументы**

* *Expr*: Строка выражение, представляющее IPv4, которые будут преобразованы в длинные. Строка может включать чистую маску с помощью [IP-префикса обозначения.](#ip-prefix-notation)

### <a name="ip-prefix-notation"></a>Нотация IP-префикса

Это обычная практика для определения `IP-prefix notation` IP-адресов с использованием слэйс ( )`/`характер.
IP-адрес к LEFT слэша ()`/`является базовым IP-адресом, а число (от 1 до 32) к ПРАВУ слэша (/) — это число смежных 1 битов в сетчатой маске. Таким образом, 192.168.2.0/24 будет иметь связанную сеть/поднетную маску, содержащую 24 смежных бита или 255.255.0 в пунктирном десятичном формате.

**Возвращает**

Если конверсия будет успешной, результат будет длинным числом.
Если конверсия не будет `null`успешной, результат будет .
 
**Пример**

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