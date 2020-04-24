---
title: аго() - Исследователь данных Azure (ru) Документы Майкрософт
description: Эта статья описывает назад () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7d155f1a1cd113f73acc779e6c2e73d5f537e71c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519097"
---
# <a name="ago"></a>ago()

Вычитает заданный интервал времени из текущего времени UTC.

```kusto
ago(1h)
ago(1d)
```

Как и `now()`, эту функцию можно использовать в выражении несколько раз, и значение времени UTC будет каждый раз одинаковым.

**Синтаксис**

`ago(`*a_timespan*`)`

**Аргументы**

* *a_timespan* — интервал для вычитания из текущего времени UTC (`now()`).

**Возвращает**

`now() - a_timespan`

**Пример**

Все строки за последний час:

```kusto
T | where Timestamp > ago(1h)
```