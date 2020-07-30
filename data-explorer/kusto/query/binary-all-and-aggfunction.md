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
ms.openlocfilehash: 9086d00ecbc800174ce2b9cda2b4ae1ba59d52b5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349148"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and () (агрегатная функция)

Накапливает значения с помощью бинарной `AND` операции для группы формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `binary_all_and(` *выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: длинное число.

## <a name="returns"></a>Результаты

Возвращает значение, агрегированное с помощью двоичной `AND` операции над записями для группы формирования сводных данных (или в целом, если формирование сводных данных выполняется без группирования).

## <a name="example"></a>Пример

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
