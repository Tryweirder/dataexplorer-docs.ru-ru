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
ms.openlocfilehash: 1f5584233a24c834e0ca6c28ed60aa5d7496b411
ms.sourcegitcommit: 284152eba9ee52e06d710cc13200a80e9cbd0a8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2020
ms.locfileid: "86291531"
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

В следующем примере показано, как получить список всех сегментов данных с записями за час назад с заданным значением для столбца `ActivityId` . В нем показано, что некоторые операторы запросов (здесь, `where` оператор, а также `extend` и `project` ) сохраняют сведения о сегменте данных, где размещается запись.

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
