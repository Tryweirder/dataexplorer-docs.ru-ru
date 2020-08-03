---
title: Значения NULL — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются значения NULL в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c493431fcfa22ad0419659a5b6e036205f3bf299
ms.sourcegitcommit: 194453a8eb11c3ccb54c473e887c84cb8e91b939
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87473978"
---
# <a name="null-values"></a>Значения NULL

Все скалярные типы данных в Kusto имеют специальное значение, представляющее отсутствующее значение.
Это значение называется **значением NULL**или просто значением **null**.

## <a name="null-literals"></a>Литералы NULL

Значение NULL скалярного типа `T` представлено на языке запросов литералом NULL `T(null)` .
Таким образом, следующий метод возвращает одну строку, заполненную значениями NULL:

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> Обратите внимание, что в настоящее время `string` тип не поддерживает значения NULL.

## <a name="comparing-null-to-something"></a>Сравнение значения NULL с каким-либо

Значение NULL не сравнивается со значением, равным любому другому значению типа данных, включая само себя. (То есть `null == null` имеет значение false.) Чтобы определить, является ли какое-либо значение значением NULL, используйте функцию [IsNull ()](../isnullfunction.md) и функцию [isnotnull ()](../isnotnullfunction.md) .

## <a name="binary-operations-on-null"></a>Бинарные операции со значением NULL

Как правило, NULL ведет себя в «прикрепленном» виде вокруг бинарных операторов; двоичная операция между значением NULL и любым другим значением (включая другое значение null) создает значение null.

## <a name="data-ingestion-and-null-values"></a>Прием данных и значения NULL

Для большинства типов данных отсутствующее значение в источнике данных создает значение NULL в соответствующей ячейке таблицы. Исключением являются столбцы типа `string` и приема, подобного CSV, где значение Missing возвращает пустую строку.
Например, если у нас есть: 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

Затем сделайте следующее:

|a     |b     |IsNull (a)|IsEmpty (a)|strlen (a)|IsNull (b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* Если выполнить приведенный выше запрос в Kusto. Explorer, все `true` значения будут отображаться как `1` , а все `false` значения будут отображаться как `0` .

::: zone-end

* Kusto не предлагает способ ограничить столбец таблицы значениями NULL (иными словами, не существует эквивалента `NOT NULL` ограничения SQL).
