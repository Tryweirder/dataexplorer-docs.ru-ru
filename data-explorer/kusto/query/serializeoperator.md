---
title: оператора сериализации - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается оператор сериализации в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5402d1e1fcceb42f02643bf24918ed07beddaed7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509118"
---
# <a name="serialize-operator"></a>Оператор serialize

Отметки, что порядок набора строк ввода является безопасным для использования оконных функций.

Оператор имеет декларативное значение, и он отмечает набор строк ввода как сериализованный (упорядоченный), чтобы [функции окна](./windowsfunctions.md) могли быть применены к нему.

```kusto
T | serialize rn=row_number()
```

**Синтаксис**

`serialize`*Name1* `=` *Expr1* `,` - *Name2* `=` *Expr2*

* Пары *Name*/*Expr* аналогичны парам в [операторе расширения.](./extendoperator.md)

**Пример**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

Набор строк вывода следующих операторов помечен как сериализованный:

[диапазон,](./rangeoperator.md) [сортировка,](./sortoperator.md) [заказ,](./orderoperator.md) [топ-хиттеров,](./tophittersoperator.md) [getschema](./getschemaoperator.md). [top](./topoperator.md)

Набор строк вывода следующих операторов помечен как несерийный:

[образец](./sampleoperator.md), [образец-отличный](./sampledistinctoperator.md), [различные](./distinctoperator.md), [присоединиться](./joinoperator.md), [топ-вложенных](./topnestedoperator.md), [кол,](./countoperator.md) [обобщить](./summarizeoperator.md), [аспект](./facetoperator.md), [mv-расширить](./mvexpandoperator.md), [оценить,](./evaluateoperator.md) [уменьшить,](./reduceoperator.md) [сделать-серии](./make-seriesoperator.md)

Все остальные операторы сохраняют свойство сериализации (если набор строк ввода сериализован, то и набор строк вывода).