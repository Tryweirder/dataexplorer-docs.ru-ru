---
title: оператор Sort в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор Sort в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4f9878b77ad2288395a54d5315864e460ca37875
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351035"
---
# <a name="sort-operator"></a>Оператор sort 

Сортирует по порядку строки входной таблицы (по одному или нескольким столбцам).

```kusto
T | sort by strlen(country) asc, price desc
```

**Псевдоним**

`order`

## <a name="syntax"></a>Синтаксис

*T* `| sort by` *выражение* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>Аргументы

* *T*: входные данные таблицы для сортировки.
* *выражение*: скалярное выражение, по которому выполняется сортировка. Значения должны быть представлены числовыми или строковыми значениями, значениями времени или даты.
* `asc` — сортировка по возрастанию. `desc` — сортировка по убыванию. Используется по умолчанию.
* `nulls first`(по умолчанию для `asc` Order) значения null будут помещаться в начало и `nulls last` (по умолчанию для `desc` Order) в конце будут помещаться значения NULL.

## <a name="example"></a>Пример

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

Все строки в таблице трассировки с определенным значением `ActivityId`, отсортированные по меткам времени. Если `Timestamp` столбец содержит значения NULL, то они будут отображаться в первых строках результата.

Чтобы исключить значения NULL из результата, добавьте фильтр перед вызовом sort:

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```