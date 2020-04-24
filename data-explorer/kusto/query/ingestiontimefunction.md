---
title: ingestion_time () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ingestion_time () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2474b8751be5cba2270bcbd2936c76b91f5f3ba0
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030045"
---
# <a name="ingestion_time"></a>ingestion_time()

Извлекает `$IngestionTime` скрытый `datetime` столбец записи или значение null.
`$IngestionTime` Столбец определяется автоматически, когда таблица

::: zone pivot="azuredataexplorer"

[Политика инжестионтиме](../management/ingestiontimepolicy.md) установлена (включена).

::: zone-end

::: zone pivot="azuremonitor"

Политика Инжестионтиме установлена (включена).

::: zone-end

Если в таблице не определена эта политика, возвращается значение null.

Эта функция должна использоваться в контексте реальной таблицы для возврата соответствующих данных. Например, он будет возвращать значение NULL для всех записей, если оно вызывается после `summarize` оператора.

**Синтаксис**

 `ingestion_time()`

**Возвращает**

`datetime` Значение, указывающее примерное время приема в таблицу.

**Пример**

```kusto
T 
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
