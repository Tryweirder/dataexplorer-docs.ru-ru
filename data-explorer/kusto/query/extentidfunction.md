---
title: extent_id () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается extent_id () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: eb1984ba80b5d2940591428fea4b1f6c3982f9d9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246622"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

Возвращает уникальный идентификатор, определяющий сегмент данных ("экстент"), в котором находится текущая запись.

Применение этой функции к вычисляемым данным, которые не присоединены к сегменту данных, возвращает пустой GUID (все нули).

## <a name="syntax"></a>Синтаксис

`extent_id()`

## <a name="returns"></a>Результаты

Значение типа `guid` , идентифицирующее сегмент данных текущей записи или пустой GUID (все нули).

## <a name="example"></a>Пример

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
