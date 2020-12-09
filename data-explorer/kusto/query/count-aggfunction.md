---
title: dcount() (статистическая функция) в Azure Data Explorer | Документация Майкрософт
description: В этой статье описана функция count() (статистическая функция) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.localizationpriority: high
ms.openlocfilehash: e45510b893d6e84f029764aa9fdac0d326a96f94
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513103"
---
# <a name="count-aggregation-function"></a>count() (статистическая функция)

Возвращает количество записей в группе формирования сводных данных (или общее количество, если формирование сводных данных выполняется без группирования).

* Функцию можно использовать только в контексте статистической обработки в операторе [summarize](summarizeoperator.md).
* С помощью статистической функции [countif](countif-aggfunction.md) можно подсчитать только записи, для которых некоторые предикаты возвращают `true`.

## <a name="syntax"></a>Синтаксис

summarize `count()`

## <a name="returns"></a>Возвращаемое значение

Возвращает количество записей в группе формирования сводных данных (или общее количество, если формирование сводных данных выполняется без группирования).

## <a name="example"></a>Пример

Подсчет событий в штатах, начинающихся с буквы `W`:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|Состояние|Count|
|---|---|
|ЗАПАДНАЯ ВИРДЖИНИЯ|757|
|ВАЙОМИНГ|396|
|ВАШИНГТОН|261|
|ВИСКОНСИН|1850|
