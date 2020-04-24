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
ms.openlocfilehash: 5ccb3c73bb51033fb55c60c35468a5909e87e104
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030010"
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

Это не поддерживается в Azure Monitor

::: zone-end
