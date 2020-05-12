---
title: binary_all_and () (агрегатная функция) — обозреватель данных Azure
description: В этой статье описывается binary_all_and () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9f0e1665010885a64e6d97151b074d3a03df829b
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227576"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and () (агрегатная функция)

Накапливает значения с помощью бинарной `AND` операции для группы формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

**Синтаксис**

суммировать `binary_all_and(` *выражение*`)`

**Аргументы**

* *Expr*: длинное число.

**Возвращает**

Возвращает значение, агрегированное с помощью двоичной `AND` операции над записями для группы формирования сводных данных (или в целом, если формирование сводных данных выполняется без группирования).

**Пример**

Создание "кафе-Food" с помощью бинарных `AND` операций:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0xFFFFFFFF, 
  0xFFFFF00F,
  0xCFFFFFFD,
  0xFAFEFFFF,
]
| summarize result = toupper(tohex(binary_all_and(num)))
```

|result|
|---|
|CAFEF00D|
