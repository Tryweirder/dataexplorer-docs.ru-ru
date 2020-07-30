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
ms.openlocfilehash: 35478b435814fe716f7130576c16714403490be6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349131"
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

|result|
|---|
|CAFEF00D|
