---
title: format_ipv4 () — обозреватель данных Azure
description: В этой статье описывается format_ipv4 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.openlocfilehash: 5f941a640ffc0bf30859509cf3e3eb1235f464ff
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88075410"
---
# <a name="format_ipv4"></a>format_ipv4 ()

Анализирует входные данные с помощью маски сети и возвращает строку, представляющую IPv4-адрес.

```kusto
print format_ipv4('192.168.1.255', 24) == '192.168.1.0'
print format_ipv4(3232236031, 24) == '192.168.1.0'
```

## <a name="syntax"></a>Синтаксис

`format_ipv4(`*Expr* [ `,` *префиксмаск*`])`

## <a name="arguments"></a>Аргументы

* *`Expr`*: Строковое или числовое представление IPv4-адреса.
* *`PrefixMask`*: (Необязательно) целое число от 0 до 32, представляющее количество наиболее значимых битов, которые учитываются в учетной записи. Если аргумент не указан, используются все битовые маски (32).

## <a name="returns"></a>Возвращаемое значение

Если преобразование выполнено успешно, результатом будет строка, представляющая IPv4-адрес.
Если преобразование не выполнено, результатом будет пустая строка.

## <a name="see-also"></a>Дополнительно

- [format_ipv4_mask ()](format-ipv4-mask-function.md): для форматирования IPv4-адресов, включая нотацию CIDR.
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
