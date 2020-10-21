---
title: extent_tags () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается extent_tags () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: da705d558a09bdcc52bf07fc807e53fdccb9396c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249091"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

Возвращает динамический массив с [тегами](../management/extents-overview.md#extent-tagging) сегмента данных ("экстент"), в котором находится текущая запись. 

Применение этой функции к вычисляемым данным, которые не присоединены к сегменту данных, возвращает пустое значение.

## <a name="syntax"></a>Синтаксис

`extent_tags()`

## <a name="returns"></a>Результаты

Значение типа `dynamic` , которое является массивом, содержащим теги экстента текущей записи, или пустое значение.

## <a name="examples"></a>Примеры

В следующем примере показано, как получить список тегов всех сегментов данных, имеющих записи за час назад, с заданным значением для столбца `ActivityId` . В нем показано, что некоторые операторы запросов (здесь `where` оператор, но это также верно для `extend` и `project` ) сохраняют сведения о сегменте данных, где размещается запись.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

В следующем примере показано, как получить количество всех записей за последний час, которые хранятся в экстентах, помеченных тегом `MyTag` (и потенциально другими тегами), но не помечены тегом `drop-by:MyOtherTag` .

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
