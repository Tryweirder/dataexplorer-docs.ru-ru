---
title: binary_all_or () (агрегатная функция) — обозреватель данных Azure
description: В этой статье описывается binary_all_or () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: bff5d631493b9431d6e1a5fa88d3dd419147df96
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247974"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or () (агрегатная функция)

Накапливает значения с помощью бинарной `OR` операции для группы формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `binary_all_or(` *выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: длинное число.

## <a name="returns"></a>Результаты

Возвращает значение, агрегированное с помощью двоичной `OR` операции над записями для группы формирования сводных данных (или в целом, если формирование сводных данных выполняется без группирования).

## <a name="example"></a>Пример

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

|набор по|
|---|
|CAFEF00D|
