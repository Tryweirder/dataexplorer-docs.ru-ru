---
title: Оператор данных - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор datatable в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 182f8030e3263ee5bf6bee4ca7444b0d5596e7d6
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765399"
---
# <a name="datatable-operator"></a>Оператор DataTable

Возвращает таблицу, схема и значения которой определены в самом запросе.

Обратите внимание, что для этого оператора ввод конвейера не предусмотрен.

**Синтаксис**

`datatable``(` *КолонкаНалиподназвание* `:` *КолонкаТип* `,` `)` *ScalarValue* `,` *ScalarValue* ScalarValue »ScalarValue ...» `[``]`

**Аргументы**

::: zone pivot="azuredataexplorer"

* *ColumnName*, *ColumnType*: Они определяют схему таблицы. Используемый Syntax точно такой же, как синтаксис, используемый при определении таблицы (см. [таблицу .create).](../management/create-table-command.md)
* *ScalarValue:* постоянное значение масштабирования для вставки в таблицу. Количество значений должно быть целым числом нескольких столбцов в таблице, а значение *n*'th должно иметь тип, соответствующий столбцов *n* % *NumColumns.*

::: zone-end

::: zone pivot="azuremonitor"

* *ColumnName*, *ColumnType*: Они определяют схему таблицы.
* *ScalarValue:* постоянное значение масштабирования для вставки в таблицу. Количество значений должно быть целым числом нескольких столбцов в таблице, а значение *n*'th должно иметь тип, соответствующий столбцов *n* % *NumColumns.*

::: zone-end

**Возвращает**

Этот оператор возвращает таблицу данных данной схемы и данных.

**Пример**

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
