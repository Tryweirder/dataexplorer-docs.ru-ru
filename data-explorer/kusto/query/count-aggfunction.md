---
title: Count () (агрегатная функция) — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается Count () (агрегатная функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.localizationpriority: high
ms.openlocfilehash: e45510b893d6e84f029764aa9fdac0d326a96f94
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513103"
---
# <a name="count-aggregation-function"></a>Count () (агрегатная функция)

Возвращает количество записей в группе формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)
* Функция [СЧЁТЕСЛИ](countif-aggfunction.md) агрегации используется для подсчета только тех записей, для которых возвращается некоторый предикат `true` .

## <a name="syntax"></a>Синтаксис

SUMMARIZE `count()`

## <a name="returns"></a>Возвращаемое значение

Возвращает количество записей в группе формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

## <a name="example"></a>Пример

Подсчет событий в состояниях, начинающихся с буквы `W` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|Состояние|Count|
|---|---|
|ЗАПАДНАЯ ВИРДЖИНИЯ|757|
|ШТАТ ВАЙОМИНГ|396|
|Вашингтон|261|
|ВИСКОНСИНА|1850|
