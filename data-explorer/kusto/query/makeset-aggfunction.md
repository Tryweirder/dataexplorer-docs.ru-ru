---
title: make_set () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_set () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 0ae1a01af019e18e8e9f05454a1c52ef6a1f856c
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103088"
---
# <a name="make_set-aggregation-function"></a>make_set () (агрегатная функция)

Возвращает массив `dynamic` (JSON) с набором различных значений, которые выражение *Expr* принимает в группе.

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

`summarize``make_set(` *Expr* [ `,` *MAXSIZE*]`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение для вычисления агрегата.
* *MAXSIZE* — Необязательное целочисленное ограничение на максимальное число возвращаемых элементов (по умолчанию — *1048576*). Значение MaxSize не может превышать 1048576.

> [!NOTE]
> Устаревший и устаревший вариант этой функции: `makeset()` имеет ограничение по умолчанию *MaxSize* = 128.

## <a name="returns"></a>Результаты

Возвращает массив `dynamic` (JSON) с набором различных значений, которые выражение *Expr* принимает в группе.
Порядок сортировки массива не определен.

> [!TIP]
> Для подсчета только уникальных значений используйте [DCount ()](dcount-aggfunction.md) .

## <a name="example"></a>Пример

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="Таблица с запросом Kusto. суммирование стран по континенту в Azure обозреватель данных":::

## <a name="see-also"></a>См. также раздел

* Используйте [`mv-expand`](./mvexpandoperator.md) оператор для противоположной функции.
* [`make_set_if`](./makesetif-aggfunction.md) аналогичен оператору `make_set` , за исключением того, что он также принимает предикат.
