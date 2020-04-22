---
title: Значения null - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описаны значения null в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c3a48fdfca855a1ff2f848d4ed97d8162e97b931
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765956"
---
# <a name="null-values"></a>Значения NULL

Все типы масштабирования данных в Kusto имеют особое значение, представляющее недостающее значение.
Это значение называется **нулевая стоимость,** или просто **нулевой**.

## <a name="null-literals"></a>Нулевые буквальные

Нулевая стоимость типа `T` scalar представлена в языке запроса нулевой буквальной. `T(null)`
Таким образом, следующий возвращает один ряд, полный нулей:

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> Обратите внимание, `string` что в настоящее время тип не поддерживает значения null.

## <a name="comparing-null-to-something"></a>Сравнение нулевой к чему-то

Нулевое значение не сравнивается с любым другим значением типа данных, включая себя. (То есть, `null == null` является ложным.) Чтобы определить, является ли какое-либо значение нулевого значения, используйте функцию [isnull()](../isnullfunction.md) и функцию [isnotnull()](../isnotnullfunction.md) .

## <a name="binary-operations-on-null"></a>Двоичные операции на нулевой

В общем, null ведет себя «липким» образом вокруг бинарных операторов; двоичная операция между нулевую стоимость и любым другим значением (включая другое нулевое значение) производит нулевую величину.

## <a name="data-ingestion-and-null-values"></a>Проглатывание данных и нулевая стоимость

Для большинства типов данных недостающее значение в источнике данных производит нулевую величину в соответствующей ячейке таблицы. Исключением являются столбцы типа `string` и CSV-подобный проглатывание, где отсутствующее значение производит пустую строку.
Так, например, если у нас есть: 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

Затем сделайте следующее:

|а     |b     |isnull (a)|isempty(a)|strlen (a)|isnull (b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |Да      |0        |Да     |
|&nbsp;|&nbsp;|false    |false     |1        |Да     |
|а     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* Если `true` вы запустите запрос выше в Kusto.Explorer, все значения `1`будут рассеяны как, и все `false` значения будут отображаться как `0`.

::: zone-end

* Kusto не предлагает способ ограничить столбец таблицы от нулевых значений (другими словами, нет `NOT NULL` эквивалента ограничению S'L).
