---
title: binary_all_xor () (агрегатная функция) — обозреватель данных Azure
description: В этой статье описывается binary_all_xor () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: c2fab7abbcebdc97f6f1c394f840bf6e232db03a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349114"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor () (агрегатная функция)

Накапливает значения с помощью бинарной `XOR` операции для группы формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `binary_all_xor(` *выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: длинное число.

## <a name="returns"></a>Результаты

Возвращает значение, агрегированное с помощью двоичной `XOR` операции над записями для группы формирования сводных данных (или в целом, если формирование сводных данных выполняется без группирования).

## <a name="example"></a>Пример

Создание "кафе-Food" с помощью бинарных `XOR` операций:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0x44404440,
  0x1E1E1E1E,
  0x90ABBA09,
  0x000B105A,
]
| summarize result = toupper(tohex(binary_all_xor(num)))
```

|result|
|---|
|CAFEF00D|
