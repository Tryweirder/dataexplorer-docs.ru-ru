---
title: make_timespan() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны make_timespan () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31301f684ea700cf89e9234c4c43adab068319b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512773"
---
# <a name="make_timespan"></a>make_timespan()

Создает значение масштабирования [времени](./scalar-data-types/timespan.md) от указанного периода времени.

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

**Синтаксис**

`make_timespan(`*час*,*минута*`)`

`make_timespan(`*час*,*минута*,*второй*`)`

`make_timespan(`*день*,*час*,*минута*,*второй*`)`

**Аргументы**

* *день*: день (положительное значение integer)
* *час*: час (значение всей теплы, от 0 до 23)
* *минута*: минута (значение всей величины, от 0 до 59)
* *второй*: второй (реальное значение, от 0 до 59.9999999)

**Возвращает**

Если создание будет успешным, результат будет значение [промежуток времени,](./scalar-data-types/timespan.md) в противном случае, результат будет недействительным.
 
**Пример**

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|Интервал времени|
|---|
|1.12:30:55.1230000|


