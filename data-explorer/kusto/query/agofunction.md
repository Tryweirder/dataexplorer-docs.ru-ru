---
title: назад () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается назад () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4457f7e400922221e139011508e13d4d7e6ee551
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247044"
---
# <a name="ago"></a>ago()

Вычитает заданный интервал времени из текущего времени UTC.

```kusto
ago(1h)
ago(1d)
```

Как и `now()`, эту функцию можно использовать в выражении несколько раз, и значение времени UTC будет каждый раз одинаковым.

## <a name="syntax"></a>Синтаксис

`ago(`*a_timespan*`)`

## <a name="arguments"></a>Аргументы

* *a_timespan* — интервал для вычитания из текущего времени UTC (`now()`).

## <a name="returns"></a>Результаты

`now() - a_timespan`

## <a name="example"></a>Пример

Все строки за последний час:

```kusto
T | where Timestamp > ago(1h)
```