---
title: оператор Order — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор Order в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f552143be8c02cece19030fc7b6f79d5a4bdf4a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241383"
---
# <a name="order-operator"></a>Оператор order 

Сортирует по порядку строки входной таблицы (по одному или нескольким столбцам).

```kusto
T | order by country asc, price desc
```

> [!NOTE]
> Оператор Order является псевдонимом для оператора Sort. Дополнительные сведения см. в разделе [оператор Sort](sortoperator.md) .

## <a name="syntax"></a>Синтаксис

*T* `| order by` *столбец* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>Аргументы

* *T*: входные данные таблицы для сортировки.
* *Column*: столбец объекта *T* , по которому выполняется сортировка. Значения должны быть представлены числовыми или строковыми значениями, значениями времени или даты.
* `asc` — сортировка по возрастанию. `desc` — сортировка по убыванию. Используется по умолчанию.
* `nulls first` (по умолчанию для `asc` Order) значения null будут помещаться в начало и `nulls last` (по умолчанию для `desc` Order) в конце будут помещаться значения NULL.

