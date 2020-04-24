---
title: оператор сортировки - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор сортировки в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 638783b28cddc51d64a80096d7d4d6e0f669d354
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507486"
---
# <a name="sort-operator"></a>Оператор sort 

Сортирует по порядку строки входной таблицы (по одному или нескольким столбцам).

```kusto
T | sort by strlen(country) asc, price desc
```

**Псевдоним**

`order`

**Синтаксис**

`asc` | `desc` *Т-выражение* `| sort by` *expression* :`nulls first` | `nulls last``,`

**Аргументы**

* *T*: Ввод таблицы для сортировки.
* *выражение*: Scalar выражение которым, которым сортировать. Значения должны быть представлены числовыми или строковыми значениями, значениями времени или даты.
* `asc` — сортировка по возрастанию. `desc` — сортировка по убыванию. Используется по умолчанию.
* `nulls first`(по умолчанию для `asc` ордера) разместит `nulls last` значения null `desc` в начале и (по умолчанию для заказа) разместит значения null в конце.

**Пример**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

Все строки в таблице трассировки с определенным значением `ActivityId`, отсортированные по меткам времени. Если `Timestamp` столбец содержит нулевые значения, они будут отображаться на первых строках результата.

Чтобы исключить значения null из результата, добавьте фильтр перед вызовом для сортировки:

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```