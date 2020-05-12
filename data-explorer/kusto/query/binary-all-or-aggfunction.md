---
title: binary_all_or () (агрегатная функция) — обозреватель данных Azure
description: В этой статье описывается binary_all_or () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: e00d170db7cbafb36f04dfeb14f64caf2b8abcff
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225264"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or () (агрегатная функция)

Накапливает значения с помощью бинарной `OR` операции для группы формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

**Синтаксис**

суммировать `binary_all_or(` *выражение*`)`

**Аргументы**

* *Expr*: длинное число.

**Возвращает**

Возвращает значение, агрегированное с помощью двоичной `OR` операции над записями для группы формирования сводных данных (или в целом, если формирование сводных данных выполняется без группирования).

**Пример**

Создание "кафе-Food" с помощью бинарных `OR` операций:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0x88888008,
  0x42000000,
  0x00767000,
  0x00000005, 
]
| summarize result = toupper(tohex(binary_all_or(num)))
```

|result|
|---|
|CAFEF00D|
