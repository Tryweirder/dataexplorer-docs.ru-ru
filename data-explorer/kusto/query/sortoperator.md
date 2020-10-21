---
title: оператор Sort в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор Sort в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8823b0a6bb15898a9bb15ed00919fa57d75f8e25
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245806"
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
* `nulls first` (по умолчанию для `asc` Order) значения null будут помещаться в начало и `nulls last` (по умолчанию для `desc` Order) в конце будут помещаться значения NULL.

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