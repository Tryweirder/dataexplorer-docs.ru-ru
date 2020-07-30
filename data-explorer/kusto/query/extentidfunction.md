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
ms.openlocfilehash: 22f1b36b851c6e629abd2524feb4c40c74bbb1fa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348145"
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
