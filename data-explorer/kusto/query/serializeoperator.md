---
title: Сериализация оператора в Azure обозреватель данных
description: В этой статье описывается оператор Serialize в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 708a5ccd5f8402dedb074a6ab8c17b1d7762839c
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717127"
---
# <a name="serialize-operator"></a>Оператор serialize

Помечает, что порядок входных строк безопасности можно использовать в качестве оконных функций.

Оператор имеет декларативное значение. Он помечает входной набор строк как сериализованный (упорядоченный), чтобы к нему можно было применить [функции окон](./windowsfunctions.md) .

```kusto
T | serialize rn=row_number()
```

**Синтаксис**

`serialize`[*Name1* `=` *Выражение1* [ `,` *имя2* `=` *Expr2*]...]

* Пары *Name* / *expr* Name аналогичны этим парам в [операторе Extend](./extendoperator.md).

**Пример**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

Набор выходных строк следующих операторов помечен как сериализованный.

[диапазон](./rangeoperator.md), [Сортировка](./sortoperator.md), [порядок](./orderoperator.md), [верхний](./topoperator.md), [верхний — hitters](./tophittersoperator.md), [GetSchema](./getschemaoperator.md).

Набор выходных строк следующих операторов помечается как несериализуемый.

[Пример](./sampleoperator.md), [Пример-DISTINCT](./sampledistinctoperator.md), [DISTINCT](./distinctoperator.md), [Join](./joinoperator.md), [нисходящее вложение](./topnestedoperator.md), [количество](./countoperator.md), [Сводка](./summarizeoperator.md), [аспект](./facetoperator.md), [MV — развернуть](./mvexpandoperator.md), [оценить](./evaluateoperator.md), [сократить на](./reduceoperator.md), [создать ряд](./make-seriesoperator.md)

Все остальные операторы сохраняют свойство Serialization. Если входной набор строк сериализуется, то также сериализуется набор выходных строк.
