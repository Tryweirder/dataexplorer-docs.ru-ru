---
title: extent_tags () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается extent_tags () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 146ab59c1c0cbcb86bfae94fa26c09f5afa0f216
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737595"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

Возвращает динамический массив с [тегами](../management/extents-overview.md#extent-tagging) сегмента данных ("экстент"), в котором находится текущая запись. 

Применение этой функции к вычисляемым данным, которые не присоединены к сегменту данных, возвращает пустое значение.

**Синтаксис**

`extent_tags()`

**Возвращает**

Значение типа `dynamic` , которое является массивом, содержащим теги экстента текущей записи, или пустое значение.

**Примеры**

В следующем примере показано, как получить список тегов всех сегментов данных, имеющих записи за час назад, с заданным значением для столбца `ActivityId`. В нем показано, что некоторые операторы запросов (здесь `where` оператор, но это также верно для `extend` и `project`) сохраняют сведения о сегменте данных, где размещается запись.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

В следующем примере показано, как получить количество всех записей за последний час, которые хранятся в экстентах, помеченных тегом `MyTag` (и потенциально другими тегами), но не помечены тегом. `drop-by:MyOtherTag`

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
