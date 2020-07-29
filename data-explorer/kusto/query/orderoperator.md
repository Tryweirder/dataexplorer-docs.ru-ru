---
title: оператор Order — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор Order в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 35fa0c29bf00a8696d4e9d08d73c30bba6171ea1
ms.sourcegitcommit: de81b57b6c09b6b7442665e5c2932710231f0773
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87264721"
---
# <a name="order-operator"></a>Оператор order 

Сортирует по порядку строки входной таблицы (по одному или нескольким столбцам).

```kusto
T | order by country asc, price desc
```

> [!NOTE]
> Оператор Order является псевдонимом для оператора Sort. Дополнительные сведения см. в разделе [оператор Sort](sortoperator.md) .

**Синтаксис**

*T* `| order by` *столбец* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

**Аргументы**

* *T*: входные данные таблицы для сортировки.
* *Column*: столбец объекта *T* , по которому выполняется сортировка. Значения должны быть представлены числовыми или строковыми значениями, значениями времени или даты.
* `asc` — сортировка по возрастанию. `desc` — сортировка по убыванию. Используется по умолчанию.
* `nulls first`(по умолчанию для `asc` Order) значения null будут помещаться в начало и `nulls last` (по умолчанию для `desc` Order) в конце будут помещаться значения NULL.

