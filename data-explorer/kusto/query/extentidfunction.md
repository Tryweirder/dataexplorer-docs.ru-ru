---
title: extent_id () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается extent_id () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 300f7961fd11b433ef4e420d5a20b9ad9150b269
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737629"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

Возвращает уникальный идентификатор, определяющий сегмент данных ("экстент"), в котором находится текущая запись. 

Применение этой функции к вычисляемым данным, которые не присоединены к сегменту данных, возвращает пустой GUID (все нули).

**Синтаксис**

`extent_id()`

**Возвращает**

Значение типа `guid` , идентифицирующее сегмент данных текущей записи или пустой GUID (все нули).

**Пример**

В следующем примере показано, как получить список всех сегментов данных с записями за час назад с заданным значением для столбца `ActivityId`. В нем показано, что некоторые операторы запросов (здесь `where` оператор, но это также верно для `extend` и `project`) сохраняют сведения о сегменте данных, где размещается запись.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend eid=extent_id()
| summarize by eid
```

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
