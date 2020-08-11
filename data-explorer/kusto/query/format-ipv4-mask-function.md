---
title: format_ipv4_mask () — обозреватель данных Azure
description: В этой статье описывается format_ipv4_mask () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.openlocfilehash: ef0b8306ad5d62e7efa9cb037a6fb8d06d415859
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88075426"
---
# <a name="format_ipv4_mask"></a>format_ipv4_mask ()

Анализирует входные данные с помощью маски сети и возвращает строку, представляющую IPv4-адрес в нотации CIDR.

```kusto
print format_ipv4_mask('192.168.1.255', 24) == '192.168.1.0/24'
print format_ipv4_mask(3232236031, 24) == '192.168.1.0/24'
```

## <a name="syntax"></a>Синтаксис

`format_ipv4_mask(`*Expr* [ `,` *префиксмаск*`])`

## <a name="arguments"></a>Аргументы

* *`Expr`*: Строковое или числовое представление IPv4-адреса в виде нотации CIDR.
* *`PrefixMask`*: (Необязательно) целое число от 0 до 32, представляющее количество наиболее значимых битов, которые учитываются в учетной записи. Если аргумент не указан, используются все битовые маски (32).

## <a name="returns"></a>Возвращаемое значение

Если преобразование выполнено успешно, результатом будет строка, представляющая IPv4-адрес в нотации CIDR.
Если преобразование не выполнено, результатом будет пустая строка.

## <a name="see-also"></a>Дополнительно

- [format_ipv4 ()](format-ipv4-function.md): для форматирования IPv4-адресов без нотации CIDR.
- [Функции IPv4 и IPv6](scalarfunctions.md#ipv4ipv6-functions)

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(address:string, mask:long)
[
 '192.168.1.1', 24,          
 '192.168.1.1', 32,          
 '192.168.1.1/24', 32,       
 '192.168.1.1/24', long(-1), 
]
| extend result = format_ipv4(address, mask), 
         result_mask = format_ipv4_mask(address, mask)
```

|address|маска|result|result_mask|
|---|---|---|---|
|192.168.1.1|24|192.168.1.0|192.168.1.0/24|
|192.168.1.1|32|192.168.1.1|192.168.1.1/32|
|192.168.1.1/24|32|192.168.1.0|192.168.1.0/24|
|192.168.1.1/24|-1|||
