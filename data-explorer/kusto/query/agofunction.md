---
title: назад () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается назад () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f3d58a7b29731125407eec7429967d1f90f5b7e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349777"
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