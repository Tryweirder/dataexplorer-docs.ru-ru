---
title: сейчас () - Исследователь данных Azure (ru) Документы Майкрософт
description: Эта статья описывает сявра () в исследователе данных Лазурных данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c1a130cfbd45c35ff1ba26ed6c47986fc186c89c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512059"
---
# <a name="now"></a>now()

Возвращает текущее время часов UTC, дополнительно компенсируемое заданным временным span.
Эта функция может использоваться в выражении несколько раз, и значение времени будет одинаковым для всех случаев.

```kusto
now()
now(-2d)
```

**Синтаксис**

`now(`-*смещение*`)`

**Аргументы**

* *смещения*: A `timespan`, добавлено к текущему времени часов UTC. По умолчанию: 0.

**Возвращает**

Текущее время UTC в формате `datetime`.

`now()` + *Смещение* 

**Пример**

Определяет интервал с момента события, определенного предикатом.

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```