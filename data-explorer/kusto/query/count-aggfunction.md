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
ms.openlocfilehash: a5f5f554373331d66a08e7166249e8e24c4fbd7c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348791"
---
# <a name="count-aggregation-function"></a>Count () (агрегатная функция)

Возвращает количество записей в группе формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)
* Функция [СЧЁТЕСЛИ](countif-aggfunction.md) агрегации используется для подсчета только тех записей, для которых возвращается некоторый предикат `true` .

## <a name="syntax"></a>Синтаксис

SUMMARIZE`count()`

## <a name="returns"></a>Результаты

Возвращает количество записей в группе формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

## <a name="example"></a>Пример

Подсчет событий в состояниях, начинающихся с буквы `W` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|Состояние|Счетчик|
|---|---|
|ЗАПАДНАЯ ВИРДЖИНИЯ|757|
|ШТАТ ВАЙОМИНГ|396|
|Вашингтон|261|
|ВИСКОНСИНА|1850|
