---
title: СЧЁТЕСЛИ () (агрегатная функция) — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описываются функции СЧЁТЕСЛИ () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/02/2020
ms.openlocfilehash: 9e81b4947f3a3a0b1102256cb7fd2f635ce4611b
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614987"
---
# <a name="countif-aggregation-function"></a>СЧЁТЕСЛИ () (агрегатная функция)

Возвращает число строк, для которых предикат *Predicate* принимает значение `true`. Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md).

## <a name="syntax"></a>Синтаксис

Итоговый `countif(` *предикат*`)`

## <a name="arguments"></a>Аргументы

*Predicate*: выражение, которое будет использоваться для вычисления агрегата. *Предикат* может быть любым скалярным выражением с типом возвращаемого значения bool (вычисление значения true или false).

## <a name="returns"></a>Возвращаемое значение

Возвращает число строк, для которых предикат *Predicate* принимает значение `true`.

## <a name="example"></a>Пример

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize countif(strlen(name) > 4)
```

|countif_|
|----|
|2|

## <a name="see-also"></a>См. также раздел

Функция [Count ()](count-aggfunction.md) , которая подсчитывает строки без выражения предиката.
