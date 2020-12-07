---
title: make_set () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_set () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 6e57f22dae1abef3838ed6f7065759c4a203e389
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303298"
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
> `makeset()` — Это устаревшая и устаревшая версия функции `make_set` . Для устаревшей версии используется ограничение по умолчанию *MAXSIZE* = 128.

## <a name="returns"></a>Возвращаемое значение

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
