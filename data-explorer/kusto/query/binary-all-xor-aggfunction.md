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
ms.openlocfilehash: bc4b0bc8a02dd3a8d2a39ffdd27db5817eb8ffdb
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225247"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor () (агрегатная функция)

Накапливает значения с помощью бинарной `XOR` операции для группы формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

**Синтаксис**

суммировать `binary_all_xor(` *выражение*`)`

**Аргументы**

* *Expr*: длинное число.

**Возвращает**

Возвращает значение, агрегированное с помощью двоичной `XOR` операции над записями для группы формирования сводных данных (или в целом, если формирование сводных данных выполняется без группирования).

**Пример**

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
