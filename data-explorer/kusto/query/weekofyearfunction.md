---
title: week_of_year () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается week_of_year () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 18cad42dfd0f652daa4c8da80524ba40ace9b153
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251232"
---
# <a name="week_of_year"></a>week_of_year ()

Возвращает целое число, представляющее номер недели. Номер недели вычисляется с первой недели года, которая включает первый четверг в соответствии с [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).

```kusto
week_of_year(datetime("2015-12-14"))
```

## <a name="syntax"></a>Синтаксис

`week_of_year(`*a_date*`)`

## <a name="arguments"></a>Аргументы

* `a_date` — значение в формате `datetime`.

## <a name="returns"></a>Результаты

`week number` — Номер недели, который содержит указанную дату.

## <a name="examples"></a>Примеры

|Входные данные                                    |Выходные данные|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()` является устаревшим вариантом этой функции. `weekofyear()` не соответствует стандарту ISO 8601; Первая неделя года была определена как неделя с первой в году.
Текущей версией этой функции `week_of_year()` является соответствие стандарту ISO 8601. Первая неделя года определяется как неделя с первым четверг года в этом году.