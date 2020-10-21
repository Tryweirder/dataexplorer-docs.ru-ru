---
title: Now () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается Now () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 63b99b4774c46872b83e526ca00a5d974f0b0c19
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249811"
---
# <a name="now"></a>now()

Возвращает текущее время в формате UTC, при необходимости смещается на заданный интервал времени.
Эта функция может использоваться в выражении несколько раз, и значение времени будет одинаковым для всех случаев.

```kusto
now()
now(-2d)
```

## <a name="syntax"></a>Синтаксис

`now(`[*offset*]`)`

## <a name="arguments"></a>Аргументы

* *offset*: а `timespan` , добавленное к текущему времени UTC. По умолчанию: 0.

## <a name="returns"></a>Результаты

Текущее время UTC в формате `datetime`.

`now()` + *собой* 

## <a name="example"></a>Пример

Определяет интервал с момента события, определенного предикатом.

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```