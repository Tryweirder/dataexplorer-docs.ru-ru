---
title: week_of_year () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны week_of_year () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 1c3702165f01ab321f80bad900e59968092be2ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504545"
---
# <a name="week_of_year"></a>week_of_year()

Возвращает целый ряд, представляющий номер недели. Недельное число рассчитывается с первой недели года, которая включает в себя первый четверг, в соответствии с [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).

```kusto
week_of_year(datetime("2015-12-14"))
```

**Синтаксис**

`week_of_year(`*a_date*`)`

**Аргументы**

* `a_date` — значение в формате `datetime`.

**Возвращает**

`week number`- Номер недели, содержащий указанную дату.

**Примеры**

|Входные данные                                    |Выходные данные|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()`является устаревшим вариантом этой функции. `weekofyear()`ISO 8601 не соответствовала требованиям; первая неделя года была определена как неделя с первой средой года в нем.
Текущая версия этой `week_of_year()`функции, iSO 8601 совместима; первая неделя года определяется как неделя с первым четвергом года в нем.