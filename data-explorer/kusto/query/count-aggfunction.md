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
ms.openlocfilehash: 6a06be43773a356e903b25b2697e75b8342ed7f8
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133482"
---
# <a name="count-aggregation-function"></a>Count () (агрегатная функция)

Возвращает количество записей в группе формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)
* Функция [СЧЁТЕСЛИ](countif-aggfunction.md) агрегации используется для подсчета только тех записей, для которых возвращается некоторый предикат `true` .

**Синтаксис**

SUMMARIZE`count()`

**Возвращает**

Возвращает количество записей в группе формирования сводных данных (или, в целом, если формирование сводных данных выполняется без группирования).

**Пример**

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
