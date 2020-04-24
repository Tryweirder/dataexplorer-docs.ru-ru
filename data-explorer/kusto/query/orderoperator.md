---
title: Оператор заказа - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор заказов в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9e2c2adb86f1eb705856e95f8b8f4ee329cb3b43
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511974"
---
# <a name="order-operator"></a>Оператор order 

Сортирует по порядку строки входной таблицы (по одному или нескольким столбцам).

```kusto
T | order by country asc, price desc
```

**Псевдоним**

[Оператор sort](sortoperator.md)

**Синтаксис**

`asc` | `desc``nulls first` | `nulls last``,` *T* `| sort by` *Т-колонка* , и я не й ...»

**Аргументы**

* *T*: Ввод таблицы для сортировки.
* *колонка*: Колонка *T,* по которой сортировать. Значения должны быть представлены числовыми или строковыми значениями, значениями времени или даты.
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