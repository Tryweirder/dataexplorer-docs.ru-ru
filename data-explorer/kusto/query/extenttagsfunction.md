---
title: extent_tags () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны extent_tags() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d8af7e51c5e2efb16763541db05e9ccc7e2cb95f
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765431"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

Возвращает динамический массив с [тегами](../management/extents-overview.md#extent-tagging) осколок агтизма данных ("extent"), в котором находится текущая запись. 

Применение этой функции к рассчитанным данным, не прикрепленным к осколоку данных, возвращает пустое значение.

**Синтаксис**

`extent_tags()`

**Возвращает**

Значение типа, `dynamic` которое представляет собой массив, удерживая теги размера текущей записи, или пустое значение.

**Примеры**

Ниже приводится следующий пример, как получить список тегов всех осколков данных, `ActivityId`которые имеют записи час назад с определенным значением для столбца. Это показывает, что некоторые операторы `where` запросов (здесь, оператор, но это также верно для `extend` и) `project`сохранить информацию о осколке данных хостинг записи.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

В следующем примере показано, как получить количество всех записей за последний час, которые `MyTag` хранятся в размерах, которые помечены `drop-by:MyOtherTag`тегом (и, возможно, другими тегами), но не помечены тегом.

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
