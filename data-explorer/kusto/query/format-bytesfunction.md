---
title: format_bytes () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны format_bytes () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5d5bfa234e001f498737b9df88274096f8592f52
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515170"
---
# <a name="format_bytes"></a>format_bytes()

Форматы числа как строки, представляющей размер данных в байтах.

```kusto
format_bytes(1024) == '1 KB'"
```

**Синтаксис**

`format_bytes(`*значение* `,` -`,` *точность* и *единицы*`)`

**Аргументы**

* `value`: число, подавнее форматировать размер данных в байтах.
* `precision`: (необязательно) Количество цифр, к которые будет округлено значение. (значение по умолчанию 0).
* `units`: (необязательно) Единицы целевого размера данных, `MB`которые `GB` `TB`будет использовать форматирование строки ( `PB``Bytes`, `KB`, , ). Если параметр пуст - единицы будут автоматически подобраны на основе вхотворного значения.

**Возвращает**

Строка с результатом формата.

**Примеры**

```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|Версия 1|Версия 2|Версия 3|версия 4|v5|
|---|---|---|---|---|
|564 Байт|10.1 КБ|19 Mb|19.08 Мб|19541 КБ|