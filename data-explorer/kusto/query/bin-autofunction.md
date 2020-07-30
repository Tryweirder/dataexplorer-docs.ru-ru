---
title: bin_auto () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается bin_auto () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6df5d9793f2d076eb8f97156e911fb49aba4cc9c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349165"
---
# <a name="bin_auto"></a>bin_auto()

Округляет значения до фиксированного размера "bin", контролируя размер ячейки и начальную точку, предоставляемые свойством запроса.

## <a name="syntax"></a>Синтаксис

`bin_auto``(` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* *Выражение*: скалярное выражение числового типа, которое указывает округляемое значение.

**Свойства запроса клиента**

* `query_bin_auto_size`: Числовой литерал, указывающий размер каждой ячейки.
* `query_bin_auto_at`: Числовой литерал, указывающий одно значение *выражения* , которое является "фиксированной точкой" (то есть значение, `fixed_point` для которого `bin_auto(fixed_point)` == `fixed_point` .)

## <a name="returns"></a>Результаты

Ближайшее `query_bin_auto_at` выражение, кратное приведенному ниже *выражению*, смещается так, чтобы `query_bin_auto_at` оно было преобразовано в само себя.

## <a name="examples"></a>Примеры

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|Timestamp                    | count_|
|-----------------------------|-------|
|2017-01-01 00:05:00.0000000  | 60    |
|2017-01-01 01:05:00.0000000  | 56    |